---
layout: post
title: SoapUI - Groovy script
date: 03-04-2025
categories: [DevOps, Tools, Scripts]
tags: [soapUI, groovy, scripting]
---


## Summary

On daily basis we face operational challenges that require `scripting` or quick solution that does the job effeciently. In this case, we have a kernel system that does the job of below groovy script; however, it was misbehaving for certain cases. As a result, I needed a quick work-around to be fully automated in order to fix onging issue in our legacy system. 

> #### Script objective
Execute set of SOAP message commands based on a lookup table. 


## Script

```groovy 
import groovy.xml.XmlUtil
import java.nio.file.*
import groovy.util.XmlSlurper

def testStepName = "Get - xx-QueryStaticData"  // Change to match your SOAP request test step name
def sessionID = context.expand('${#Project#sessionID}')  // Get session ID from project property - must be logged in before you trigger this command

def results = new StringBuilder()
def filePath = "<<your full path>>_results.txt"  //<< must be changed as desired


// internal list of numbers with status failure + error code [[internal error code we're getting]] numbers to iterate through 
def numbers = []

// Map to hold first 8 digits as key and a corresponding value of our internal loookup table
def lookupTable = [
"9xxxxxxxx": "CBSxxxxxxxxxxx",
"9xxxxxxxx": "CBSxxxxxxxxxxx",
"9xxxxxxxx": "CBSxxxxxxxxxxx",


]

// Iterate through numbers and fetch corresponding values
numbers.each { num ->
    def msisdn=num
    def key = num.take(8) // Get first 8 digits
    def value = lookupTable.get(key, "Unknown") // Lookup value or default to "Unknown"

    log.info("Number: $msisdn -> First 8 Digits: $key -> Mapped Value: $value")


// this is where you paste your command skelleton. 

def createAF = """[[[Your full body request with ${varibale} annotation is required]]]"""

def deleteAIR="""[[[Your full body request with ${varibale} annotation is required]]]"""

def deleteAF="""[[[Your full body request with ${varibale} annotation is required]]]"""

def deletewithKI="""[[[Your full body request with ${varibale} annotation is required]]]"""



    // Set request payload based on the saop message above
    testRunner.testCase.testSteps[testStepName].testRequest.setRequestContent(createAF)

    // Execute the SOAP request
    def responseCreateAF  = testRunner.testCase.testSteps[testStepName].run(testRunner, context).responseContent
    
	def parsedXmlcreateAF = new XmlSlurper().parseText(responseCreateAF) 
    
	//2-payload - deleteAIR
	testRunner.testCase.testSteps[testStepName].testRequest.setRequestContent(deleteAIR)
	def responsedeleteAIR  = testRunner.testCase.testSteps[testStepName].run(testRunner, context).responseContent
	
	def parsedXmldeleteAIR = new XmlSlurper().parseText(responsedeleteAIR)
	
	//3-payload - deleteAF
	testRunner.testCase.testSteps[testStepName].testRequest.setRequestContent(deleteAF)
	def responsedeleteAF  = testRunner.testCase.testSteps[testStepName].run(testRunner, context).responseContent
	
	def parsedXmldeleteAF = new XmlSlurper().parseText(responsedeleteAF)
	
	//4-payload - deletewithKI
	testRunner.testCase.testSteps[testStepName].testRequest.setRequestContent(deletewithKI)
	def responsedeletewithKI  = testRunner.testCase.testSteps[testStepName].run(testRunner, context).responseContent
	
	
	def parsedXmldeletewithKI = new XmlSlurper().parseText(responsedeletewithKI)
	 
	
	
	log.info("Parsed response CreateAF: ${parsedXmlcreateAF} \n")
	log.info("Parsed response deleteAIR: ${parsedXmldeleteAIR} \n")
	log.info("Parsed response deleteAF: ${parsedXmldeleteAF}  \n")
	log.info("Parsed response deleteHLR: ${parsedXmldeletewithKI} \n")

	results.append(" \n")
	results.append(" ---------------START------------------- ")
	results.append(" start processing --> ${msisdn} : ${value} \n")
	results.append("Parsed response CreateAF: ${parsedXmlcreateAF} \n")
	results.append("Parsed response deleteAIR: ${parsedXmldeleteAIR} \n")
	results.append("Parsed response deleteAF: ${parsedXmldeleteAF}  \n")
	results.append("Parsed response deleteHLR: ${parsedXmldeletewithKI} \n")
	results.append(" finish processing --> ${msisdn} : ${value} \n")
	results.append(" ---------------END------------------- ")
	results.append(" \n")

    
}
// Save results to a file
Files.write(Paths.get(filePath), results.toString().getBytes(), StandardOpenOption.CREATE, StandardOpenOption.APPEND)
log.info "Results saved to: ${filePath}"


```


>*Note: * I've removed sensitive data and naming from the script as this script is internal and serves its purpose for operational purposes. 



>## Tip  
Always test with a sigle test data beforehand.


## References
