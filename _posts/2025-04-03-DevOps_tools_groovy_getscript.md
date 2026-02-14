---
layout: post
title: SoapUI - Groovy Batch Get Script via SOAP based API
date: 03-04-2025
categories: [DevOps, Tools, Scripts]
tags: [soapUI, groovy, scripting]
published: false
---


## Summary

Our Kernel tool didn't have batch get command by default; as a result, we needed a simple lightweight and portable solution, this is where this script came handy to all team members. I use it to make sure the telecom IMSI is reflected on our HLR correctly in problematic cases. 

> #### Script objective
to make sure the telecom IMSI is reflected on our HLR correctly in problematic cases.


## Script

```groovy 
import groovy.xml.XmlUtil
import java.nio.file.*
import groovy.util.XmlSlurper

def testStepName = "Get - xxx-QueryStaticData"  //This is in case you have a companion SOAP message
def msisdnList = [""] // This is a list we will iterate
def sessionID = context.expand('${#Project#sessionID}')  // Get session ID from project property

def results = new StringBuilder()
def filePath = "<<PATH>>/queryHLR.txt"  // Change file path as needed

msisdnList.each {
	msisdn ->log.info "Processing MSISDN: ${msisdn}"

    // Construct SOAP request dynamically
    def soapRequest = """<?xml version="1.0" encoding="UTF-8"?>
    <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" 
                     <<<Sensored>>>">
       <soapenv:Header>
          <cai3:SessionId>${sessionID}</cai3:SessionId>
       </soapenv:Header>
       <soapenv:Body>
          <cai3:Get>
             <cai3:MOType><<<Sensored>>></cai3:MOType>
             <cai3:MOId>
                <sensored:msisdn>${msisdn}</sensored:msisdn>
             </cai3:MOId>
             <cai3:MOAttributes>
               <sensored:get >
	          <sensored:msisdn>${msisdn}</sensored:msisdn>
	          <sensored:isIMSI>1</sensored:isIMSI>
	        </sensored:get>
             </cai3:MOAttributes>
          </cai3:Get>
       </soapenv:Body>
    </soapenv:Envelope>"""

    // Set request payload
    testRunner.testCase.testSteps[testStepName].testRequest.setRequestContent(soapRequest)

    // Execute the SOAP request
    def response = testRunner.testCase.testSteps[testStepName].run(testRunner, context).responseContent
   
	
    // Parse the XML response  
    def parsedXml = new XmlSlurper().parseText(response)  

    def allRecords = parsedXml[0]
    log.info(allRecords)

	// Regular expressions for extracting IMSI and ISDN based on response
	def imsiPattern = /IMSI\s*=\s*(\d+)/
	def isdnPattern = /ISDN\s*=\s*(\d+)/

	// Extract IMSI
	def imsiMatch = (response =~ imsiPattern)
	def imsi = imsiMatch.find() ? imsiMatch.group(1) : "IMSI not found"

	// Extract ISDN
	def isdnMatch = (response =~ isdnPattern)
	def isdn = isdnMatch.find() ? isdnMatch.group(1) : "ISDN not found"

	// Print results in SoapUI log
	log.info ("Extracted IMSI: ${imsi}, ISDN: ${isdn}")

    
    results.append("Result: LOOKUP: ${msisdn} - IMSI: ${imsi} -  ISDN: ${isdn} \n") 
    
}

// Save results to a file
Files.write(Paths.get(filePath), results.toString().getBytes(), StandardOpenOption.CREATE, StandardOpenOption.APPEND)
log.info "Results saved to: ${filePath}"

```


>*Note: * I've removed sensitive data and naming from the script as this script is internal and serves its purpose for operational purposes. Please check sensored tags and remove accordignly. This script serves as a `generic telecom` script now. 



>## Tip  
Always test with a sigle test data beforehand.


## References
