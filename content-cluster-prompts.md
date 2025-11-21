# Content Cluster Analysis Prompts

This document contains all prompts used in the Content Cluster Analysis feature. All prompts have been improved with better guardrails, explicit definitions, and stricter output constraints.

---

## 1. Search Intent Analysis Prompt
**Location:** Line 3074  
**Purpose:** Classifies webpage content into search intent categories

```
You are an SEO search-intent classifier. Classify the primary search intent of the webpage content below.

Valid intents (choose exactly ONE):
- Informational: user seeking knowledge or education.
- Commercial: user comparing options, evaluating solutions, or researching before purchase.
- Navigational: user trying to reach a specific brand, page, or product.
- Transactional: user ready to take an action such as buying, signing up, downloading, or requesting a quote.

Return ONLY the intent name with no explanation, no punctuation, and no extra text.

Content:
${contentPreview}
```

**Improvements:**
- Added guardrails to prevent multi-label outputs
- Made definitions explicit so the model isn't guessing
- Reinforced "return only the intent" with strict formatting requirements

---

## 2. Cluster Consolidation Analysis Prompt
**Location:** Line 3269  
**Purpose:** Analyzes URLs in a cluster to determine why they should be consolidated

```
You are an expert in SEO content consolidation and content pruning.

Analyze the provided cluster data and explain WHY these URLs should be consolidated into a single page. Base your reasoning strictly on:
- Overlapping topics
- Keyword similarity
- Redundant content
- Search intent alignment
- Thin or duplicative value signals

Do NOT repeat the cluster data. Do NOT list the URLs individually. Summarize the justification at a high but accurate level.

Return ONLY JSON in this structure:
{
  "consolidationReason": "explanation"
}

Cluster Data:
${JSON.stringify(clusterData, null, 2)}
```

**Improvements:**
- Prevents the model from rewriting cluster data
- Makes the JSON schema required and enforces "no extra fields"
- Clarifies what "consolidation" means with explicit criteria

---

## 3. Content Recommendations Prompt
**Location:** Line 3366  
**Purpose:** Provides content recommendations for improving pages related to a seed topic

```
You are an SEO content strategist. Analyze the URLs and their ranking keywords for the seed topic "${seedTopic}".

Provide recommendations focusing ONLY on:
1. Content gaps (missing information, subtopics, or depth)
2. Optimization improvements (structure, clarity, depth, on-page SEO)
3. Keyword targeting enhancements (internal linking, alignment with search intent, semantically related terms)

When referencing years, ALWAYS use: ${currentYear}.

Return ONLY JSON in this structure:
{
  "recommendations": [
    {
      "url": "url",
      "recommendations": ["rec 1", "rec 2"]
    }
  ],
  "generalRecommendations": ["rec 1", "rec 2"]
}

Data:
${JSON.stringify(recommendationsData, null, 2)}
```

**Improvements:**
- Reinforced JSON determinism
- Avoided fluff with tighter recommendation categories
- Prevented year hallucination with explicit instruction

---

## 4. New Content Opportunities Prompt
**Location:** Line 3425  
**Purpose:** Identifies new content opportunities based on existing content and seed topic

```
You are an SEO content strategist. Based on the seed topic "${seedTopic}" and the existing content below, identify NEW content opportunities that fill demonstrable gaps.

Focus ONLY on:
1. Missing intents (e.g., commercial pages missing, or lack of transactional pages)
2. Sub-topics with significant search demand or user value
3. Complementary pages that expand topical authority
4. Ideas that do NOT duplicate existing content

Return ONLY JSON in this structure:
{
  "newContentIdeas": [
    {
      "title": "Content title/topic",
      "intent": "Informational|Commercial|Navigational|Transactional",
      "targetKeywords": ["keyword1", "keyword2"],
      "rationale": "Why this content should be created"
    }
  ]
}

Existing Content Summary:
- URLs analyzed: ${urls.length}
- Intent distribution: ${Object.entries(intentGroups).map(([intent, urls]) => `${intent}: ${urls.length}`).join(', ')}
- Related keywords: ${relatedKeywords.slice(0, 20).join(', ')}

Data:
${JSON.stringify(newContentData, null, 2)}
```

**Improvements:**
- Added harder constraints so the model doesn't generate generic ideas
- Made it reference real gaps relative to the seed topic
- Ensured it selects intent rationally

---

## 5. Page Summary Prompt
**Location:** Line 3774  
**Purpose:** Generates concise summaries of webpage content

```
Summarize the main topic and content of this webpage in 2–3 concise sentences. Focus ONLY on the primary subject matter and the core points discussed. Do NOT include assumptions or information not present in the text.

Content:
${resultsByUrl[url].rawContent.substring(0, 4000)}

Return the summary only:
```

**Improvements:**
- Ensured 2–3 sentences max
- Made it more "SEO summary" than generic summary
- Added constraints to prevent hallucinations

---

## 6. Cluster Labeling Prompt
**Location:** Line 3844  
**Purpose:** Analyzes a cluster of pages and generates cluster metadata (name, description, tags, intent, funnel stages)

```
You are an SEO and information-architecture strategist.

Analyze the pages in this cluster. Infer the unifying concept and produce structured metadata for the cluster. Follow these rules:
- Cluster name: 2–5 words, human-readable, suitable for navigation.
- Description: 1–2 clear sentences describing what the cluster covers.
- Topic tags: 3–8 canonical SEO-friendly tags (no duplicates, no brand names).
- Dominant intent: choose ONE (Informational, Commercial, Transactional, Navigational, Local).
- Funnel stages: choose 1–2 (Awareness, Consideration, Decision, Retention).

Return ONLY this JSON:
{
  "clusterName": "Cluster Name",
  "description": "1-2 sentence description",
  "topicTags": ["tag1", "tag2", "tag3"],
  "dominantIntent": "Informational|Commercial|Transactional|Navigational|Local",
  "funnelStages": ["Awareness", "Consideration"]
}

Pages in cluster:
${JSON.stringify(clusterPages, null, 2)}
```

**Improvements:**
- Added stronger constraints on naming
- Ensured the cluster name is short and business-facing
- Reinforced that tags must be canonical, not random

---

## 7. Page Topic Analysis Prompt
**Location:** Line 3928  
**Purpose:** Analyzes individual pages to extract topic, intent, audience, and expertise level

```
Analyze the webpage summary and extract the following:

1. Primary topic (1–3 words; e.g., "Email Automation", "Local SEO", "Mortgage Rates")
2. Primary intent (Informational, Commercial, Transactional, Navigational, Local)
3. Target audience (e.g., "Content Marketers", "Small Business Owners", "Developers")
4. Expertise level (Beginner, Intermediate, Advanced)

Return ONLY this JSON:
{
  "topic": "Main Topic",
  "primaryIntent": "Informational|Commercial|Transactional|Navigational|Local",
  "audience": "Target Audience",
  "expertiseLevel": "Beginner|Intermediate|Advanced"
}

Page Summary:
${resultsByUrl[url].summary}
```

**Improvements:**
- Clarified that "topic" is not a headline
- Reduced hallucination risk
- Improved audience inference with examples

---

## Summary

The Content Cluster Analysis feature uses **7 distinct prompts**, all improved with:

1. **Search Intent Analysis** - Classifies content intent with explicit definitions and strict output format
2. **Cluster Consolidation Analysis** - Explains why URLs should be consolidated with clear criteria
3. **Content Recommendations** - Provides optimization recommendations with focused categories
4. **New Content Opportunities** - Identifies content gaps and new ideas with constraints against generic suggestions
5. **Page Summary** - Generates concise page summaries with hallucination prevention
6. **Cluster Labeling** - Creates cluster metadata and taxonomy with strict naming rules
7. **Page Topic Analysis** - Extracts topic, intent, audience, and expertise level with clear examples

All prompts are designed to work with OpenAI's API and return structured JSON responses where applicable. Each prompt includes guardrails to prevent multi-label outputs, hallucinations, and ensure deterministic JSON responses.
