---
layout: post
title: False Recall (Roediger & McDermott Replication)
image: "/posts/false_recall_image-img.png"

tags: [Psychology, Experiment, R, jsPsych, HTML, JavaScript]
---

# Table of contents
- [01. Project Overview](#overview)
   - [Context](#context)
   - [Research Questions & Hypotheses](#rq)
- [02. Experimental Design](#design)
- [03. Implementation](#implementation)
- [04. Results](#results)
- [05. Discussion](#discussion)
- [06. Reflection](#reflection)
- [07. Try the Experiment](#try)

---

# Project Overview  <a name="overview-main"></a>

This project replicates Roediger & McDermott’s (1995) classic experiment on false memory formation using semantically associated word lists. The goal was to investigate how often people mistakenly recognize or recall words that were never presented, but are related to studied material.

### Context <a name="context "></a>
This study examines false recognition in the DRM paradigm, where semantically related study lists can lead to endorsement of unpresented critical lures as “old.” The project aims to replicate core findings from Roediger & McDermott (1995) and quantify recognition performance and confidence across item types (studied, critical, weakly related, unrelated). The planned design specifies within-subjects exposure to six 12-word lists, a randomized recognition test, and dependent measures of recognition accuracy, false recognition rate, and confidence ratings on a 4-point scale. 

### Research Questions & Hypotheses <a name="rq"></a>
* RQ1. Are critical lures falsely recognized more often than unrelated or weakly related items?
* H1. False “old” rates will be higher for critical lures than for unrelated lures, consistent with spreading activation accounts of semantic association.
* RQ2. Do confidence ratings for critical lures approach those for studied items and exceed those for weak/unrelated items?
* H2. Mean confidence will follow: studied ≳ critical > weak ≈ unrelated.

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
* The experiment was developed using jsPsych, JavaScript, and HTML
* Data were automatically logged at the trial level in CSV format, with columns including participant_id, list_id, word, word_type, response, likert_response, and rt.
   * Word types: {studied, critical, weakly related, unrelated}
   * Responses: { old, new }
   * Confidence ratings: {"Definitely New", "Probably New", "Probably Old", "Definitely Old"}
* All lists, word orders, and recognition stimuli were randomized for each participant to control for order effects.
* During the recognition phase, participants provided both a recognition response and a confidence rating for each item.
* Data were analyzed in R, using the tidyverse for data wrangling and ggplot2 for visualization.

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
![alt text](/img/posts/mean_confidence-img.png "Mean Confidence Rating by Word Type Plot")

*Figure 1.* **Average confidence by word type** (0–3 scale; error bars = **SE**).  
Studied (**M = 2.11, SE = 0.0485**) > Critical (**M = 1.93, SE = 0.0689**) ≫ Weakly related (**M = 1.13, SE = 0.0416**) ≈ Unrelated (**M = 1.08, SE = 0.0429**).  
Counts: Studied = 444, Critical = 222, Weakly related = 444, Unrelated = 444.

- **Takeaway.** Confidence for **critical lures** is **close to studied items**, highlighting a strong subjective “feels-old” signal for words never shown.
- **Why it matters.** Demonstrates the classic DRM illusion: **high confidence ≠ accuracy**, with implications for eyewitness memory and learning.
- **Quality note.** Smaller SEs for weak/unrelated items indicate stable low-confidence judgments; larger SE for critical lures reflects item/participant variability.


Table 1. Mean Confidence Ratings by Word Type

| **word_type**  | **mean_response** | **sd_response** | **n** | **se_response** |
| -------------- | ----------------: | --------------: | ----: | --------------: |
| critical       |              1.93 |            1.03 |   222 |          0.0689 |
| studied        |              2.11 |            1.02 |   444 |          0.0485 |
| unrelated      |              1.08 |           0.903 |   444 |          0.0429 |
| weakly related |              1.13 |           0.877 |   444 |          0.0416 |


```r
ggplot(false_recall_data, aes(x = reorder(word, -false_recall_rate), y = false_recall_rate, fill = word_types)) +
  geom_bar(stat = "identity", color = "black", alpha = 0.8) +
  facet_wrap(~word_types, scales = "free_x") +
  labs(
    title = "False Recall Rate for Non-studied words",
    x = "Non-studied Words",
    y = "False Recall Rate (Proportion of 'Old' Responses)",
    fill = "Word Types"
  ) +
  scale_y_continuous(labels = scales::percent) +
  theme_minimal() +
  theme(
    axis.text.x = element_text(angle = 90, hjust = 1)
  )
```
![alt text](/img/posts/false_recall_non_studied_words.png "False Recall Rate for Non-studied Words")

*Figure 2.* **Per-word false “old” rate** for non-studied items, faceted by word type.  
Critical lures show the highest rates overall (e.g., **cold ≈ 85%**), while some items (e.g., **black**) are noticeably lower.

- **Takeaway.** False memory is **item-sensitive**: certain lures are especially compelling, consistent with differences in associative strength.
- **Why it matters.** Demonstrates that beyond condition means, **stimulus selection** can meaningfully shape outcomes—useful for designing robust experiments.
- **Caveat.** Per-item estimates can be noisier; with larger N and normed stimuli, item effects will stabilize.

# Discussion <a name="discussion"></a>
The results replicate a robust false memory effect. Critical lures were frequently endorsed as “old,” far exceeding unrelated and weakly related items, and were accompanied by elevated confidence relative to non-studied controls. Item-level differences (e.g., high rates for cold vs. lower rates for black) suggest that associative strength and semantic network structure modulate susceptibility. These findings align with spreading-activation and fuzzy-trace accounts, underscoring that the subjective feeling of familiarity is not a guarantee of accuracy. Practical implications span eyewitness testimony, educational practice (e.g., confusable concepts), and UX for memory-reliant tasks.

# Reflection <a name="reflection"></a>
This project was a full journey from design to coding to analysis. I learned how to build an experimental program from scratch using jsPsych, HTML, and Javascript. Through this process, I improved my technical skills and gained a deeper understanding of experimental design, including how to structure trials, conditions, and ensue data quality and a good participant experience. I also strengthened my skills in data analysis with R, applying t-tests, descriptive statistics, and data visualization to interpret the results. Because this project involved replicating a publish study, I gained first-hand insight into the replication crisis and the importance of reproductible research.

---
# Try the experiment  <a name="experiment"></a>
To run the experiment, click on the following link:

https://cogs119.github.io/group_fa24_false_recall/

