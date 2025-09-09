# My Sample Project

For IELTS practice.

## v0.1 Requirements

- A website that assists in generating English exercises.
- For v0.1, implement functionality to generate IELTS Academic Writing Task 1 prompts.
- Multi-page application with a navigation bar; all non-relevant pages are disabled.
- On the Listening page, provide a field to generate questions:
    - You can supply an optional predefined context or leave it empty.
        - Use the context for few-shot guidance.
        - If no scope is provided, select a random scope (via chain-of-thought prompting).
    - When the Generate button is pressed, send a request to the Gemini free API expecting a JSON response structure.
        - This can be handled via middleware, a callback, or a microservice.
        - On the client, use async/await with Promises.
    - On error, show an alert. On success, compile the returned data to render a graph.
- Use the Facade pattern to orchestrate the generation process:
    - GeminiClient
    - DatabaseClient
- After generation completes, persist the record to the database (Postgres or MongoDB).
- On page load, issue a GET request (limit 10) to fetch recent questions and display them in a sidebar.
    - Allow selecting a sidebar item to continue the exercise.
- The center of the page contains a text field that is resilient to app closure while maintaining performance (e.g., autosave/local persistence).
- Provide real-time word count.
- Include a button to request LLM-based grading (judge) of the answer.
- Add a Settings tab for LLM configuration (model version, prompt).
    - Apply the Factory/Builder design pattern to create the judge object.
- Show an animation while awaiting API responses.

## Architecture

- QuestionGenerator: Facade
- LLMJudge: Factory (uses LLMSetting)
- Logger: Middleware
- General settings from `.env`: Singleton
- Graph generator: callback invoked by QuestionGenerator upon receiving a response
    - Implement as a Python microservice or compile client-side
- LLMSetting: Singleton
    - Purpose: enum (question_generation | judging)
    - LLM name
    - Provider
    - Prompt
- DB Client: Middleware (local DB → frontend), Facade, Singleton
    - GET(query, limit = 10)
    - POST
        - addQuestion(id, question, answer, judge)
        - updateQuestion(id, question, answer, judge)
    - DELETE question(id)
- GeminiClient: (frontend → FastAPI server → Gemini) Middleware, Facade, Singleton
- Word count as a reactive callback (Proxy pattern?)

## Reference

https://ieltsliz.com/ielts-speaking-part-1-topics/


