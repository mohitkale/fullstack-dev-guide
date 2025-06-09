# 03. Common Prompting Techniques (Zero-shot, Few-shot, CoT)

This tutorial explores some of the most common and effective prompting techniques: Zero-shot, Few-shot, and Chain-of-Thought (CoT) prompting. Understanding these techniques will allow you to elicit more accurate and nuanced responses from Large Language Models (LLMs) for a variety of tasks.

## Table of Contents
1.  [Introduction to Prompting Techniques](#intro-techniques)
2.  [Zero-shot Prompting](#zero-shot)
    *   Definition and Concept
    *   When to Use
    *   Examples
    *   Strengths and Limitations
3.  [Few-shot Prompting](#few-shot)
    *   Definition and Concept
    *   Providing In-Context Examples
    *   Structure of Few-shot Prompts
    *   When to Use
    *   Examples
    *   Strengths and Limitations
4.  [Chain-of-Thought (CoT) Prompting](#chain-of-thought)
    *   Definition and Concept
    *   Encouraging Step-by-Step Reasoning
    *   How to Elicit CoT
    *   When to Use (Especially for Reasoning Tasks)
    *   Examples
    *   Strengths and Limitations
5.  [Comparing the Techniques](#comparing-techniques)
6.  [Tips for Effective Implementation](#tips-implementation)
7.  [Conclusion and Next Steps](#conclusion-next-steps)

---

## 1. Introduction to Prompting Techniques <a name="intro-techniques"></a>
While the basic principles of clarity and context are crucial, specific techniques can further enhance your ability to guide LLMs. These techniques provide different ways to structure your prompts based on the complexity of the task and the desired level of guidance.

## 2. Zero-shot Prompting <a name="zero-shot"></a>

*   **Definition and Concept:** Zero-shot prompting involves asking an LLM to perform a task without providing any prior examples of how to do it within the prompt itself. The model relies solely on its pre-existing training to understand and execute the instruction.

*   **When to Use:**
    *   For simple, straightforward tasks.
    *   When the LLM is likely to have been trained on similar tasks (e.g., simple summarization, translation of common phrases, general knowledge questions).
    *   As a baseline to see how well the model performs before trying more complex techniques.

*   **Examples:**
    *   *Sentiment Analysis:* `"Text: I love this new phone!\nSentiment: "` (Expecting the model to output 'Positive')
    *   *Translation:* `"Translate to French: Hello, how are you?"`
    *   *Summarization:* `"Summarize this article in one sentence: [Article Text]"`

*   **Strengths:**
    *   Simple and quick to formulate.
    *   Requires minimal prompt engineering effort for basic tasks.

*   **Limitations:**
    *   May not work well for complex, nuanced, or novel tasks.
    *   Output quality can be inconsistent if the task is ambiguous to the model.

## 3. Few-shot Prompting <a name="few-shot"></a>

*   **Definition and Concept:** Few-shot prompting involves providing the LLM with a small number of examples (typically 1 to 5, hence "few-shot") of the task within the prompt itself. These examples demonstrate the desired input-output format and style.

*   **Providing In-Context Examples:** The examples are given directly in the prompt, allowing the LLM to learn the pattern "in-context."

*   **Structure of Few-shot Prompts:**
    ```
    [Instruction/Description of Task (Optional)]
    
    Example 1 Input: ...
    Example 1 Output: ...
    
    Example 2 Input: ...
    Example 2 Output: ...
    
    Actual Input: ...
    Actual Output: [LLM generates here]
    ```

*   **When to Use:**
    *   When zero-shot performance is inadequate.
    *   For tasks requiring a specific format, style, or nuanced understanding.
    *   To guide the model on tasks it might not have seen frequently during training.
    *   When you want to steer the model towards a particular type of response.

*   **Examples:**
    *   *Categorization:* 
        `"Categorize the following items as Fruit or Vegetable.
        Item: Apple
        Category: Fruit
        Item: Carrot
        Category: Vegetable
        Item: Banana
        Category: "` (Expecting 'Fruit')

    *   *Code Generation (Simple Function):*
        `"Convert the Python comment to a short Python function.
        # Input: a list of numbers
        # Output: the sum of the numbers
        def sum_list(numbers):
          return sum(numbers)
        
        # Input: a string
        # Output: the string reversed
        def reverse_string(s):
        "` (Expecting '  return s[::-1]')

*   **Strengths:**
    *   Significantly improves performance on many tasks compared to zero-shot.
    *   Helps the model understand complex instructions and desired output formats.
    *   More adaptable to novel tasks.

*   **Limitations:**
    *   Requires more effort to craft good examples.
    *   The quality and relevance of examples are crucial.
    *   Prompts become longer, which might be a consideration for token limits or cost.
    *   Finding the optimal number and type of examples can require experimentation.

## 4. Chain-of-Thought (CoT) Prompting <a name="chain-of-thought"></a>

*   **Definition and Concept:** Chain-of-Thought (CoT) prompting is a technique that encourages the LLM to generate a series of intermediate reasoning steps before arriving at the final answer. This is particularly effective for tasks requiring arithmetic, commonsense, or symbolic reasoning.

*   **Encouraging Step-by-Step Reasoning:** Instead of just asking for the answer, you ask the model to "think step-by-step" or demonstrate the reasoning process through few-shot examples that include these steps.

*   **How to Elicit CoT:**
    *   **Zero-shot CoT:** Simply append phrases like "Let's think step by step" or "Show your work" to the question.
        `"Roger has 5 tennis balls. He buys 2 more cans of tennis balls. Each can has 3 tennis balls. How many tennis balls does he have now? Let's think step by step."`
    *   **Few-shot CoT:** Provide examples where the reasoning steps are explicitly shown.
        `"Q: Natalia sold clips to 48 of her friends. She had 300 clips in total. If each friend bought 4 clips, how many clips does she have left?
A: Natalia sold 48 * 4 = 192 clips. She started with 300 clips. So she has 300 - 192 = 108 clips left. The final answer is 108.

        Q: The cafeteria had 23 apples. If they used 20 to make lunch and bought 6 more, how many apples do they have?
A: "` (Expecting the model to generate reasoning steps)

*   **When to Use:**
    *   Arithmetic reasoning problems.
    *   Commonsense reasoning tasks.
    *   Symbolic reasoning (e.g., simple logic puzzles).
    *   Any multi-step problem where breaking it down helps.

*   **Strengths:**
    *   Significantly improves performance on reasoning-intensive tasks.
    *   Makes the model's reasoning process more interpretable.
    *   Can help identify where the model might be going wrong.

*   **Limitations:**
    *   Primarily beneficial for complex reasoning; may not add much to simple tasks.
    *   Increases the length of the output.
    *   The quality of the generated reasoning steps can vary.

## 5. Comparing the Techniques <a name="comparing-techniques"></a>

| Feature         | Zero-shot                                  | Few-shot                                       | Chain-of-Thought (CoT)                         |
|-----------------|--------------------------------------------|------------------------------------------------|------------------------------------------------|
| **Examples in Prompt** | None                                       | 1-5+ (In-context)                              | Optional (Zero-shot CoT) or with examples (Few-shot CoT) |
| **Complexity**  | Low                                        | Medium                                         | Medium to High                                 |
| **Best For**    | Simple, common tasks                       | Specific formats, novel tasks, improved accuracy | Reasoning, multi-step problems                 |
| **Effort**      | Minimal                                    | Moderate (crafting examples)                   | Moderate to High (designing reasoning steps)   |

## 6. Tips for Effective Implementation <a name="tips-implementation"></a>
*   **Start Simple:** Always try zero-shot first. If it doesn't work, move to few-shot or CoT.
*   **Quality of Examples (Few-shot):** Ensure your examples are accurate, clear, and representative of the task.
*   **Consistency (Few-shot):** Maintain a consistent format and style in your examples.
*   **Clarity of Reasoning (CoT):** If using few-shot CoT, make sure the example reasoning steps are logical and easy to follow.
*   **Experiment:** The effectiveness of these techniques can vary based on the model, the specific task, and the data. Experimentation is key.

## 7. Conclusion and Next Steps <a name="conclusion-next-steps"></a>
Zero-shot, Few-shot, and Chain-of-Thought prompting are powerful tools in your prompt engineering toolkit. By understanding when and how to use them, you can significantly enhance your interactions with LLMs and achieve more sophisticated outcomes.

In the next tutorial, we'll delve into more advanced prompting techniques, including generating structured output, role-playing, and iterative refinement.

---

⬅️ [Back to Basic Principles of Effective Prompting](../02-pe-basic-principles/README.md) | ➡️ [Next: Advanced Prompting Techniques](../04-pe-advanced-techniques/README.md) *(Placeholder)*
