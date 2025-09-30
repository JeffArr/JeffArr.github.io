---
layout: post
title: False Recall (Roediger & McDermott Replication)
image: "/posts/false_recall.jpeg"
tags: [Psychology, Experiment, R, jsPsych, HTML, JavaScript]
---

This project replicates **Roediger & McDermott’s (1995)** classic experiment on false memory formation using semantically associated word lists. The goal was to investigate how often people mistakenly recognize or recall words that were never presented, but are related to studied material.

---

### 🧠 The Big Picture
- Human memory is **fragile and reconstructive**, often filling gaps with semantically related information.  
- False recall experiments reveal how easily people can be convinced they “remember” something that never occurred.  
- Findings have implications for **eyewitness testimony**, **education**, and **misinformation on social media**.  

---

### 📋 Experimental Design
- **Within-subjects design** with 6 lists of 12 words each.  
- Each list had one **critical lure** word that was never presented.  
- After study, participants completed a **recognition test** (42 words: studied, critical lures, weakly related, unrelated).  
- Confidence ratings collected on a **4-point Likert scale** (“definitely new” → “definitely old”).  

---

### 💻 Implementation
- Built using **jsPsych**, **JavaScript**, and **HTML**.  
- Randomized list order, word order, and recognition stimuli.  
- Collected trial-level responses and confidence ratings.  

---

### 📊 Results
- **Studied words**: highest confidence (2.10 / 3).  
- **Critical lures**: second highest confidence (1.93 / 3).  
- **Unrelated words**: lowest confidence (1.08 / 3).  
- **False recall rate for critical lures**: **68.9%**, significantly higher than unrelated words (28.9%) and weakly related lures (35.4%).  
- Example: the word *“cold”* had the highest false recall (83.8%), while *“black”* had the lowest.  

![False Recall Results](/posts/false_recall_results.png)

---

### 🔍 Discussion
- Participants often **mistook unpresented but related words** as studied items.  
- This supports the **spreading activation theory** of memory: studying related words triggers mental pathways that activate unseen but related concepts.  
- Demonstrates how **convincing false memories can feel**, raising concerns for real-world contexts like courtrooms or social media misinformation.  

---

### ✨ Reflection
This project was a full journey from design → coding → analysis.  
- Learned **experiment programming** in jsPsych from scratch.  
- Strengthened skills in **data analysis with R** (t-tests, descriptive statistics, visualization).  
- Gained first-hand insight into the **replication crisis** and the importance of reproducible research.  

Despite limitations (few word lists, AI-generated distractors, synthetic voice stimuli), the project successfully replicated the DRM false memory effect.  

---

### 📎 Links

