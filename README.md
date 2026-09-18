# Luganda-Wiki-Analysis-clean-data
Clean FIles (csv files) ready for data analysis based on 1st September 2026

# Luganda Wikimedia Community Uganda Analysis data capped 1 September 2026

## Table of Contents
- [Wikimedia Community Uganda](#wikimedia-community-uganda)
- [Article-Based Items to Track](#article-based-items-to-track)
- [Areas of Concern](#areas-of-concern)
- [Issues & Fixes](#issues--fixes)
  - [Definitions](#definitions)
  - [Relationships](#relationships)
  - [What Needs to Be Fixed](#what-needs-to-be-fixed)
- [Database Schema & Main Entities](#database-schema--main-entities)
- [Wikipedia Race](#wikipedia-race)
- [Challenges So Far](#challenges-so-far)
- [Possible Graphs](#possible-graphs)
- [References & Research Links](#references--research-links)

---


![Relations in the model](img/relations.JPG)


<!-- ![Community Diagram](img/relations.JPG) -->

## Wikimedia Community Uganda

Online communities are organized around articles that connect in two primary ways:
* **Shared Categories:** Articles sharing one or more categories ($\ge 1$).
* **Inter-wiki Linking:** Articles referencing each other via internal links.

---

## Article-Based Items to Track

1. Number of users
2. Most edited articles / Number of revisions
3. Least edited articles / Number of revisions
4. Newcomers
5. Active editors (Contributors)
6. Article views
7. Medium used to view articles
8. Time of editing (Morning, Afternoon, Evening, or Night)
9. Number of redirects *(Query to determine)*
10. Number of links on page *(Query to determine)*
11. Orphaned pages *(Query to determine)*
12. Dead-end articles *(Query to determine)*

---

## Areas of Concern

1. Luganda Wikipedia

[Click here to view image](img/patterns.JPG)

---

## Issues & Fixes

### Definitions
1. **Orphan Articles:** Articles without incoming connections from other articles (including looped articles).
2. **Dead-end Articles:** Articles that contain no internal wiki-links leading to any other article.
3. **Dead Links:** Hyperlinks whose target article does not exist.

### Relationships
* **Orphan Relationship:** If $A$ links to $B$, but $B$ does not link back to $A$ (and no other page links to $A$).
* **Dead-end Relationship:** If $A$ links to $B$, but $B$ has no outgoing links to any other page.

### What Needs to Be Fixed
1. Dead-end articles
2. Dead links
3. Orphaned articles
4. High Priority: Dead-end orphaned articles

---

## Database Schema & Main Entities

<img src="img/ER-Model.JPG" alt="Image Description" width="500">



### Entities
* `Page`
* `Article`
* `Revision`
* `Citation`
* `Wiki-link`
* `Categories`
* `Country`
* `Redirect`
* `Person`

---

### Entity Attributes

#### Page
* `Id`
* `Page Title`
* `NS` (Namespace)
* `Wiki-link-ID`
* `Citation-ID`
* `Headings` (Number of sections in article)
* `Paragraphs` (Number of paragraphs)
* `Category-ID`
* `Number of Images` ($\ge 1$)
* `Date of Creation`

#### Article
* `Page-ID`
* `Title`
* `Body`

#### Revision
* `Page-ID`
* `Revision-ID`
* `Timestamp`
* `Username`
* `Comment`
* `Origin`
* `Bytes`
* `Sha1`

#### Citation
* `Id`
* `Citation`
* `Page-ID`

#### Wiki-links
* `Id`
* `Link`
* `Page-ID`

#### Categories
* `Id`
* `Category`
* `Page-ID`

#### Country
* `Id`
* `Country`
* `Person-ID`

#### Redirect
* `Id`
* `Redirect`
* `Page-ID`

#### Person
* `Id`
* `Gender`
* `Sexual orientation`
* `Date of Birth (Dob)`
* `Date of Death (Dod)`
* `Place of birth`
* `Country of origin`
* `Citizenship`
* `Page-ID`

---

## Wikipedia Race

**Concept:** Which paths of internal wiki-links allow you to navigate from a starting article to a destination article without directly searching the article name?

> *Did you know about the six degrees of separation on Wikipedia?*

### Suggested Games & Exploration
* The longest path between articles on Wikipedia.
* The shortest path between articles on Wikipedia.

---

## Challenges So Far

1. Tracking added bytes accurately
2. Tracking deleted bytes accurately
3. Identifying the district of contributors
4. Accurately attributing page views
5. Measuring article quality

---


# Wikipedia Database Schema: Analytical Outcomes & Visualizations

This document outlines the analytical insights, structural relationships, and data visualizations that can be generated using the provided multi-relational Wikipedia entity schema.

---

## 1. Schema Relationships Overview

```
 [Person] ------(1:1)-----> [Country]
    |
  (1:1)
    v
  [Page] <------(1:N)------ [Revision]
    |
    +-----------(1:N)-----> [Wiki-links] ----> [Target Page]
    |
    +-----------(1:N)-----> [Citation]
    |
    +-----------(N:M)-----> [Categories]
    |
    +-----------(1:N)-----> [Redirect]
```

---

## 2. Core Analytical Outcomes & Insights

### A. Information Reliability & Citation Quality
* **Fact-Checking Density:** Calculate the ratio of `Citation` entries to total `Paragraphs` or `Bytes` across articles to flag under-referenced or low-quality topics.
* **Source Dependency & Domain Centrality:** Identify high-use citations across multiple `Page-ID` entries to evaluate systemic reliance on specific external sources or publications.
* **Content Richness Index:** Measure article depth by combining structural indicators (`Headings`, `Paragraphs`, `Number of Images`, and `Citation` count).

### B. Editor Dynamics & Content Volatility
* **Edit Controversies & Conflict Detection:** Track high `Revision` counts within short `Timestamp` windows paired with alternating `Username` entries to spot edit wars.
* **Vandalism & Bot Pattern Analysis:** Detect instant rollbacks and automated edits by identifying matching `Sha1` file hashes across historical revisions.
* **Article Longevity & Growth Velocity:** Monitor `Bytes` progression from the `Date of Creation` to measure how rapidly articles mature.

### C. Knowledge Representation & Demographic Bias
* **Gender & Diversity Representation:** Aggregate `Person` attributes (`Gender`, `Sexual orientation`) across different `Categories` to quantify demographic disparities in coverage (e.g., historical proportion of female biographies in STEM).
* **Geographic Coverage Disparities:** Cross-reference biographical entries via `Country of origin` and `Place of birth` against global populations to uncover geographic blind spots in coverage.
* **Historical & Longevity Trends:** Group `Date of Birth (Dob)` and `Date of Death (Dod)` metrics to evaluate historical coverage distribution across eras.

### D. Network Topology & Graph Analytics
* **Page Centrality & Hub Identification:** Compute PageRank and Betweenness Centrality on `Wiki-links` to classify gateway articles versus isolated "dead-end" pages.
* **Topic Clustering & Interdisciplinarity:** Map overlap in `Categories` to see how disparate fields (e.g., *Computer Science* and *Philosophy*) connect through mutual links.
* **Redirect Alias Mapping:** Analyze `Redirect` paths to observe common search terms, synonyms, and historical naming variations.

---

## 3. Recommended Visualizations & Graphs

| Visualization Type | Key Entities / Attributes | Analytical Goal |
| :--- | :--- | :--- |
| **Directed Network Graph** | `Page`, `Wiki-links` | Map knowledge topology, core concept hubs, and article clusters. |
| **Bipartite Citation Network** | `Page`, `Citation` | Identify key external publications powering entire article networks. |
| **Revision Timeline (Line Chart)** | `Revision` (`Timestamp`, `Bytes`) | Show growth trajectory, edit spikes during major events, and stagnation. |
| **Editor Activity Heatmap** | `Revision` (`Timestamp`, `Username`) | Reveal peak collaboration windows, timezone trends, and bot behavior. |
| **Geospatial Choropleth Map** | `Person` (`Country of origin`), `Country` | Highlight geographic bias and global coverage distribution. |
| **Demographic Bar / Donut Charts** | `Person` (`Gender`, `Sexual orientation`) grouped by `Categories` | Measure diversity gaps across academic, political, and cultural domains. |
| **Quality Scatter Plot** | `Paragraphs` vs. `Citation` count | Isolate weak/unverified long text vs. dense, well-cited content. |
| **Category Treemap** | `Categories`, `Page-ID` | Display macro-level topic proportions across the platform. |

---

## 4. Derived Metrics & Formulas

1. **Citation Density Index:**
   $$\text{Citation Density} = \frac{\text{Total Citations}}{\text{Total Paragraphs}}$$

2. **Revision Volatility Score:**
   $$\text{Volatility} = \frac{\text{Total Revisions}}{\text{Days Since Creation}}$$

3. **Link Connectivity Ratio:**
   $$\text{Connectivity} = \frac{\text{Outbound Wiki-links}}{\text{Total Paragraphs}}$$



## Other Possible Graphs

1. Title vs. Revisions
2. Day vs. Time
3. Username vs. Bytes
4. Day vs. Bytes
5. Time vs. Bytes

I am messing with you. 
Please do whatever you think is right.

NB: 
  1: Just In case, 
<a href="https://wallet.wearemarz.com/pay/1e02cad6-ec2b-4bef-b788-06d792ca22dc" target="_blank" rel="noopener noreferrer">Buy me some Tea</a>


  2: <a href="https://wa.me/256783338952?text=Hello%20Wikimedia%20Community%20Uganda" target="_blank" rel="noopener noreferrer">Let's Chat on WhatsApp</a>

  3: Let's Brainstorm


---

## References & Research Links

* [Research: Knowledge Gaps Index / Measurement / Content](https://meta.wikimedia.org/wiki/Research:Knowledge_Gaps_Index/Measurement/Content)
* [Research: Page view](https://meta.wikimedia.org/wiki/Research:Page_view)
* [List of articles every Wikipedia should have](https://meta.wikimedia.org/wiki/List_of_articles_every_Wikipedia_should_have)
