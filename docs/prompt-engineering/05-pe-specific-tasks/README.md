# 05. Prompting for Specific Tasks

This tutorial focuses on applying prompt engineering principles and techniques to common tasks where Large Language Models (LLMs) excel. We'll cover strategies for text summarization, question answering, creative content generation, code generation, and more, with practical examples for each.

## Table of Contents
1.  [Introduction: Tailoring Prompts to Tasks](#intro-tailoring)
2.  [Text Summarization](#task-summarization)
    *   Types of Summaries (Abstractive vs. Extractive)
    *   Controlling Length and Detail
    *   Focusing on Key Aspects
    *   Examples
3.  [Question Answering (Q&A)](#task-qa)
    *   Open-Domain vs. Closed-Domain Q&A
    *   Providing Context for Accurate Answers
    *   Asking for Explanations
    *   Examples
4.  [Creative Content Generation](#task-creative)
    *   Story Writing, Poetry, Scripts
    *   Brainstorming and Idea Generation
    *   Setting Tone, Style, and Constraints
    *   Examples
5.  [Code Generation and Explanation](#task-code)
    *   Generating Code Snippets in Various Languages
    *   Explaining Code Functionality
    *   Debugging Assistance (with caution)
    *   Examples
6.  [Text Classification and Categorization](#task-classification)
    *   Sentiment Analysis, Topic Labeling
    *   Using Few-shot Examples for Custom Categories
    *   Examples
7.  [Translation](#task-translation)
    *   Specifying Source and Target Languages
    *   Handling Nuances and Idioms (with care)
    *   Examples
8.  [Data Extraction](#task-data-extraction)
    *   Extracting Specific Information from Text (Names, Dates, Entities)
    *   Requesting Structured Output (JSON, CSV)
    *   Examples
9.  [General Tips for Task-Specific Prompting](#general-tips-tasks)
10. [Conclusion and Next Steps](#conclusion-next-steps)

---

## 1. Introduction: Tailoring Prompts to Tasks <a name="intro-tailoring"></a>
Different tasks require different prompting approaches. While the core principles of clarity and context always apply, the specific instructions, examples, and desired output formats will vary significantly. This tutorial provides blueprints for common LLM applications.

## 2. Text Summarization <a name="task-summarization"></a>
LLMs are excellent at condensing large amounts of text into shorter summaries.

*   **Types of Summaries:**
    *   **Abstractive:** The LLM generates new sentences that capture the essence of the text (most common for LLMs).
    *   **Extractive:** The LLM pulls key sentences directly from the original text (less common for generative LLMs, but can be guided).
*   **Controlling Length and Detail:**
    *   `"Summarize the following article in 3 sentences."`
    *   `"Provide a detailed summary (around 150 words) of this text..."`
    *   `"Give me a very brief, one-paragraph overview of..."`
*   **Focusing on Key Aspects:**
    *   `"Summarize this product review, focusing on the pros and cons mentioned by the user."`
    *   `"Condense this historical account, highlighting the main causes of the event."`
*   **Examples:**
    *   `"Text: [Long article about climate change impacts]
        Summarize the key findings of this article regarding rising sea levels in a bulleted list."`
    *   `"Summarize the plot of the movie 'Inception' for someone who hasn't seen it. Keep it under 200 words."`

## 3. Question Answering (Q&A) <a name="task-qa"></a>
LLMs can answer questions based on provided context or their general knowledge.

*   **Open-Domain vs. Closed-Domain Q&A:**
    *   **Open-Domain:** Questions about general knowledge (e.g., "What is the capital of France?").
    *   **Closed-Domain:** Questions based on specific text provided in the prompt (e.g., "Based on the article above, what was the main reason for the company's success?"). This is generally more reliable.
*   **Providing Context for Accurate Answers:**
    For closed-domain Q&A, always provide the relevant text.
    *   `"Context: [Paragraph about photosynthesis]
        Question: What are the main inputs for photosynthesis according to this text?"`
*   **Asking for Explanations:**
    *   `"Why is the sky blue? Explain like I'm five."`
*   **Examples:**
    *   `"Article: [News report about a recent election]
        Who won the election described in this article?"`
    *   `"Explain the concept of general relativity in simple terms."`

## 4. Creative Content Generation <a name="task-creative"></a>
LLMs can assist in various creative writing tasks.

*   **Story Writing, Poetry, Scripts:**
    *   `"Write a short story (around 500 words) about a lonely robot who finds a friend. Set it in a futuristic city."`
    *   `"Compose a four-stanza poem about the changing seasons, in an AABB rhyme scheme."`
    *   `"Write a scene for a screenplay where two characters argue about a lost map."`
*   **Brainstorming and Idea Generation:**
    *   `"Brainstorm 5 unique business ideas for a sustainable product."`
    *   `"List 10 potential titles for a fantasy novel about dragons and ancient magic."`
*   **Setting Tone, Style, and Constraints:**
    *   Use role-playing: `"You are a witty comedian. Tell a short joke about computers."`
    *   Specify constraints: `"Write a story that does NOT include any dialogue."`
*   **Examples:**
    *   `"Generate three different taglines for a new coffee shop that emphasizes its cozy atmosphere and artisanal beans."`
    *   `"Write a product description for a new sci-fi video game, making it sound exciting and mysterious."`

## 5. Code Generation and Explanation <a name="task-code"></a>
LLMs can generate code, explain code, and sometimes assist in debugging.

*   **Generating Code Snippets in Various Languages:**
    *   `"Write a Python function that takes a list of numbers and returns the sum of all even numbers in the list."`
    *   `"Generate HTML and CSS for a simple navigation bar with three links: Home, About, Contact."`
*   **Explaining Code Functionality:**
    *   `"Explain what this JavaScript code does: [Code Snippet]"`
    *   `"Describe the purpose of the `useEffect` hook in React, with a simple example."`
*   **Debugging Assistance (with caution):**
    LLMs can sometimes spot errors or suggest fixes, but always verify their suggestions.
    *   `"I'm getting an error in this Python code: [Code with error]. Can you see what might be wrong? Error message: [Error message]"`
*   **Examples:**
    *   `"Write a SQL query to select all users from the 'users' table who registered in the last 30 days."`
    *   `"Explain the difference between `let`, `const`, and `var` in JavaScript."`

## 6. Text Classification and Categorization <a name="task-classification"></a>
Assigning labels or categories to text.

*   **Sentiment Analysis, Topic Labeling:**
    *   `"Classify the sentiment of the following customer review as Positive, Negative, or Neutral: 'The product is okay, but the shipping was very slow.'"`
    *   `"What is the main topic of this news headline: 'Global Summit Addresses Climate Change Initiatives'? Options: Politics, Environment, Technology, Sports."`
*   **Using Few-shot Examples for Custom Categories:**
    This is highly effective for custom or nuanced categories.
    `"Categorize these support tickets:
    Ticket: 'Cannot log in to my account.' Category: Account Issue
    Ticket: 'The app crashes when I click the save button.' Category: Bug Report
    Ticket: 'How do I change my subscription plan?' Category: Billing Question
    Ticket: 'The website is very slow today.' Category: "` (Expecting 'Performance Issue' or similar)
*   **Examples:**
    *   `"Is this email spam or not spam? Email: [Email Text]"`

## 7. Translation <a name="task-translation"></a>
Translating text between languages.

*   **Specifying Source and Target Languages:**
    *   `"Translate the following English text to Spanish: 'Good morning, I would like a coffee.'"`
    *   `"What is 'merci beaucoup' in English?"`
*   **Handling Nuances and Idioms (with care):**
    LLMs are getting better, but direct translations of idioms can be tricky. For critical translations, human review is often needed.
*   **Examples:**
    *   `"Translate this paragraph into German: [Paragraph Text]"`

## 8. Data Extraction <a name="task-data-extraction"></a>
Pulling specific pieces of information from unstructured text.

*   **Extracting Specific Information (Names, Dates, Entities):**
    *   `"Extract all person names mentioned in the following text: [Text]"`
    *   `"What are the key dates mentioned in this historical document: [Document Text]"`
*   **Requesting Structured Output (JSON, CSV):**
    This is very powerful for making extracted data usable.
    *   `"From the product description below, extract the product name, price, and rating. Provide the output as a JSON object with keys 'product_name', 'price', and 'rating'. Description: [Text]"`
*   **Examples:**
    *   `"Extract all email addresses from this block of text: [Text with emails]"`

## 9. General Tips for Task-Specific Prompting <a name="general-tips-tasks"></a>
*   **Be Clear About the Task:** Start by stating the specific task (summarize, translate, classify, etc.).
*   **Provide All Necessary Input:** Ensure the LLM has the text or data it needs to work on.
*   **Use Examples (Few-shot):** Especially useful when the task is nuanced or requires a specific output format not easily described.
*   **Specify Output Format:** Crucial for making the LLM's response directly usable.
*   **Iterate:** If the first attempt isn't perfect, refine your prompt based on the output.

## 10. Conclusion and Next Steps <a name="conclusion-next-steps"></a>
By understanding how to tailor your prompts for specific tasks, you can unlock a vast range of capabilities from LLMs. The key is to combine the general principles of good prompting with task-specific instructions and examples.

In our next tutorial, we'll discuss the important topic of evaluating LLM responses and the iterative process of prompt refinement in more detail.

---

⬅️ [Back to Advanced Prompting Techniques](../04-pe-advanced-techniques/README.md) | ➡️ [Next: Evaluating Responses and Iterative Refinement](../06-pe-evaluation-iteration/README.md) *(Placeholder)*
