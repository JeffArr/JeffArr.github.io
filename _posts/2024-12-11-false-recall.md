---
layout: post
title: False Recall (Roediger & McDermott Replication)
image: "/posts/false_recall_image-img.png"

tags: [Psychology, Experiment, R, jsPsych, HTML, JavaScript]
---

# Table of contents
- [00. Project Overview](#overview)
   - [Context](#context)
   - [Research Questions & Hypotheses](#rq)
   - [Key Findings (TL;DR)](#tldr)
- [01. Experimental Design](#design)
- [02. Implementation](#implementation)
- [03. Results](#results)
- [04. Discussion](#discussion)
- [05. Reflection](#reflection)
- [06. Try the Experiment](#try)

---

# Project Overview  <a name="overview-main"></a>

This project replicates Roediger & McDermott’s (1995) classic experiment on false memory formation using semantically associated word lists. The goal was to investigate how often people mistakenly recognize or recall words that were never presented, but are related to studied material.

### Context <a name="overview-context"></a>
This study examines false recognition in the DRM paradigm, where semantically related study lists can lead to endorsement of unpresented critical lures as “old.” The project aims to replicate core findings from Roediger & McDermott (1995) and quantify recognition performance and confidence across item types (studied, critical, weakly related, unrelated). The planned design specifies within-subjects exposure to six 12-word lists, a randomized recognition test, and dependent measures of recognition accuracy, false recognition rate, and confidence ratings on a 4-point scale. 

### Research Questions & Hypotheses <a name="rq"></a>
* RQ1. Are critical lures falsely recognized more often than unrelated or weakly related items?
* H1. False “old” rates will be higher for critical lures than for unrelated lures, consistent with spreading activation accounts of semantic association.
* RQ2. Do confidence ratings for critical lures approach those for studied items and exceed those for weak/unrelated items?
* H2. Mean confidence will follow: studied ≳ critical > weak ≈ unrelated.

### Key Findings (TL;DR) <a name="tldr"></a>
* False recognition (“old” %): Critical = 68.92%, Weakly related = 35.43%, Unrelated = 28.95%.
* Mean confidence (0–3): Studied = 2.10, Critical = 1.93, Weakly related = 1.13, Unrelated = 1.08.
* Per-lure pattern: “cold” shows the highest false recognition (0.838), whereas “black” is among the lowest.

# Experimental Design <a name="design"></a>
### Participants
Thirty-seven adult participants from the University of California San Diego completed the task during a regular lecture session of COGS 119: Programming for Experimental Research. Testing was computer-based and individual.

### Materials
Six DRM lists (12 words each) were selected from Roediger & McDermott (1995). Each list had a critical lure (e.g., the anger list: mad, fear, hate, …; lure = anger) that was omitted at study. Additional weakly related and unrelated items were prepared for the recognition test.

### Procedure
1. Study phase: Participants viewed (and heard) six lists, one word at a time; list order and within-list order were randomized. The critical lure was never shown.
2. Recognition test: After study, participants completed a 42-item self-paced recognition test: 12 studied items (2 per list), 6 critical lures (one per list), 12 weakly related, and 12 unrelated items. Item order was randomized.
3. Responses: For each word, participants made an old/new judgment and a confidence rating on a 4-point Likert scale (0 = Definitely New, 3 = Definitely Old).

### Design Notes
Within-subjects design. Randomization of lists, study items, and test items. Primary measures: false “old” rate by item type and mean confidence (0–3) by item type.

# Implementation <a name="implementation"></a>
* Task platform: PsychoPy, deployed to the web via Pavlovia for in-browser participation.
* Data logging: Trial-level CSV with columns such as **participant_id**, **list_id**, **word**, **word_type**, **response**, **likert_response**, rt where word_type ∈ {studied, critical, weakly related, unrelated}, response ∈ {old, new}, and likert_response ∈ {"Definitely New","Probably New","Probably Old","Definitely Old"}
* Analysis stack: R with tidyverse and ggplot2.

# Results <a name="results"></a>
* False “old” rates: Critical = 68.92%, Weakly related = 32.43%, Unrelated = 29.95%.
* Mean confidence (0–3): Studied = 2.11, Critical = 1.93, Weakly related = 1.13, Unrelated = 1.08.
* Per-lure: cold showed the highest false recognition (~83.8%); black among the lowest.

```r
library(ggplot2)
ggplot(summary_table, aes(x = word_types, y = mean_response, fill = word_types)) +
  geom_bar(stat = "identity", width = 0.5, show.legend = FALSE) +
  geom_errorbar(aes(ymin = mean_response - se_response,
                    ymax = mean_response + se_response),
                width = 0.2, color = "gray40") +
  scale_fill_brewer(palette = "Set2") +
  labs(title = "Mean Confidence Rating by Word Type",
       x = "Word Type",
       y = "Mean Confidence Rating Response") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        plot.title  = element_text(hjust = 0.5))
```
![False Recall Results](post/mean_confidence-img.png)




# Discussion <a name="discussion"></a>
The results replicate a robust false memory effect. Critical lures were frequently endorsed as “old,” far exceeding unrelated and weakly related items, and were accompanied by elevated confidence relative to non-studied controls. Item-level differences (e.g., high rates for cold vs. lower rates for black) suggest that associative strength and semantic network structure modulate susceptibility. These findings align with spreading-activation and fuzzy-trace accounts, underscoring that the subjective feeling of familiarity is not a guarantee of accuracy. Practical implications span eyewitness testimony, educational practice (e.g., confusable concepts), and UX for memory-reliant tasks.

# Reflection <a name="reflection"></a>
* Strengths: Clear within-subjects design, thorough randomization, trial-level logging, and fully reproducible R analysis and figures.
* Limitations: Modest sample size; limited number of lists; weak/unrelated words may benefit from normed selection; confidence captured on a coarse 4-point scale.
* Next steps: Increase N and lists; select stimuli using association norms; incorporate signal-detection metrics (d′, criterion) and/or mixed-effects models; add calibration plots (confidence vs. accuracy) and pre-registration.




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

