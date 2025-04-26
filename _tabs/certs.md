---
layout: page
icon: fas fa-certificate
order: 3
title: Certifications

---

<div class="timeline">

 <div class="timeline-event">
    <div class="timeline-date">2025-04-09</div>
    <div class="timeline-dot"></div>
    <div class="timeline-content">
      <img src="../assets/images/01.png" alt="Event 2 Image">
      <h3>Future Literacy Participation</h3>
      <p></p>
    </div>
  </div>

  
  <div class="timeline-event">
    <div class="timeline-dot"></div>
    <div class="timeline-date">2025-03-25</div>
    <div class="timeline-content">
      <img src="https://media.licdn.com/dms/image/v2/D4E22AQFaQ2F-m5Snjw/feedshare-shrink_1280/B4EZWgZ4AYGwAk-/0/1742152892685?e=1748476800&v=beta&t=dDmcOUbHLs0mCK6k3mXFpbhndXFNJvCGM13B-5hhjZ4" alt="Event 1 Image">
      <h3>Kakfa Certs</h3>
        <p>I completed the "Apache Kafka Essential Training: Getting Started" course by Kumaran Ponnambalam. This dense yet highly informative course lays a solid foundation in Kafka, covering topics like producers, consumers, partitions, consumer groups, and offset management.

        But here’s the real takeaway: **No course alone is enough**—pairing it with **hands-on labs** in a home setup makes all the difference!

        By deploying Kafka in my home lab, tweaking configurations, and testing real-world scenarios, I gained a deeper, practical understanding of:
        -  **Kafka’s core concepts** – message streams, partitions, and offsets
        -  **Cluster setup & broker configurations** 
        - ** Real-time data streaming use cases ** – logging, analytics, and monitoring

        Courses provide knowledge, but **hands-on practice bridges the gap between theory and real-world implementation.**

        If you're diving into Kafka, set up a home lab—it’s a game changer! Start with this informative course. </p>
    </div>
  </div>

 

  <!-- <div class="timeline-event">
    <div class="timeline-date">2024-03-01</div>
    <div class="timeline-dot"></div>
    <div class="timeline-content">
      <img src="https://i.imgur.com/Obnojvm.png" alt="Event 2 Image">
      <h3>Image Showcase</h3>
      <p>Here's an event with an accompanying image.</p>
    </div>
  </div> -->

  </div>

<style>
/* Ensure timeline is within the main content area */
#main-container {
  overflow-x: auto; /* Prevent horizontal overflow caused by wide timeline on small screens */
}

.timeline {
  position: relative;
  padding-left: 2rem;
  margin-right: 1rem; /* Add some right margin to avoid potential overlap with main content padding */
}

.timeline::before {
  content: '';
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0.75rem;
  width: 2px;
  background-color: var(--hr-border-color, #ccc); /* Fallback if Chirpy variable not available */
}

.timeline-event {
  margin-bottom: 1.5rem;
  position: relative;
}

.timeline-date {
  position: absolute;
  left: -13rem;
  width: 12rem;
  text-align: right;
  padding: 10px;
  color: var(--text-color-secondary, #777); /* Fallback */
  font-size: 0.9rem;
}

.timeline-dot {
  position: absolute;
  top: 0.25rem;
  left: 0.5rem;
  width: 0.75rem;
  height: 0.75rem;
  background-color: var(--accent-color, #007bff); /* Fallback */
  border-radius: 50%;
  border: 1px solid var(--accent-color, #007bff);
  background-clip: padding-box;
}

.timeline-content {
  padding: 0.5rem 1rem;
  border: 1px solid var(--border-color, #ddd); /* Fallback */
  border-radius: 4px;
  background-color: var(--card-bg, #f9f9f9); /* Fallback */
  padding-left: 2rem;
}

.timeline-content img {
  max-width: 100%;
  height: auto;
  margin-bottom: 0.5rem;
}

.timeline-content h3 {
  margin-top: 0;
  color: var(--heading-color, #333); /* Fallback */
  font-size: 1.1rem;
  margin-bottom: 0.25rem;
}

/* Media query for screens smaller than Chirpy's sidebar breakpoint */
@media (max-width: 991px) { /* Chirpy's typical sidebar breakpoint */
  .timeline {
    padding-left: 1rem;
    margin-right: 0; /* Remove right margin on smaller screens */
  }

  .timeline::before {
    left: 0.25rem;
  }

  .timeline-date {
    position: static;
    width: 100%;
    text-align: left;
    margin-bottom: 0.25rem;
    font-size: 0.85rem;
  }

  .timeline-dot {
    left: 0;
    top: 0.1rem;
    width: 0.5rem;
    height: 0.5rem;
  }

  .timeline-content {
    padding-left: 1.5rem;
  }
}

/* Further adjustments for very small screens */
@media (max-width: 480px) {
  .timeline-content {
    padding: 0.25rem 0.5rem;
    padding-left: 1.5rem;
  }

  .timeline-content h3 {
    font-size: 1rem;
    margin-bottom: 0.1rem;
  }
}
</style>