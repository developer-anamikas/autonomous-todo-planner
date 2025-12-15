Automated To-Do List Orchestrator

This capstone project, built using the Google Agent Development Kit (ADK), delivers a highly dynamic and interactive solution for managing complex, multi-domain to-do lists. The system transforms a static list of tasks into an autonomous, sequential workflow, intelligently delegating tasks to specialized AI agents while maintaining a clear, conversational loop with the user.

Problem Statement

Traditional AI assistants often struggle with the sequential execution of a heterogeneous task list. They typically attempt to execute all tasks in parallel or fail to maintain the state of the overall workflow. This leads to disjointed user experiences, where a human is required to constantly re-initiate the process and ensure dependencies are met.

The Automated To-Do List Orchestrator solves this by enforcing a controlled, step-by-step process. It guarantees that complex actions (like booking a flight) are handled by the right expert, confirmed by the user, and marked complete before moving to the next priority.

Architectural Design: Hierarchical & Sequential Flow

The project employs a robust hierarchical agent architecture based on the ADK's core components.

A. The Root Agent (root_agent)

The entire application runs within a SequentialAgent named root_agent. This is the top-level container that enforces the linear execution of the workflow. Its only primary sub-agent is the MasterTaskOrchestrator.

B. The Master Task Orchestrator (MTO)

The MTO is the brain of the operation. It has three primary responsibilities:

Workflow Control: Manages the sequential flow, pausing for user confirmation after every task completion (whether automated or manual).

Task Triage and Delegation: It uses a custom tool (orchestrate_task) to determine the type and status of the next task.

State Management: Directly interacts with core utility tools (add_task, mark_complete, delete_task, view_list) to manage the global state of the user's to-do list.

C. Specialized Sub-Agents

Complex tasks are delegated to five distinct Specialized Sub-Agents, each designed to be an expert in its domain:

Booking Agent: Handles travel and transport-related inquiries (flights, trains).

Shopping Agent: Manages product research, purchasing advice, and pricing comparisons.

Hotel Agent: Focuses specifically on accommodation and stay planning.

Movie Agent: Looks up showtimes and handles entertainment booking details.

Study Agent: Provides guidance, plans, and resources for educational goals (like studying for a Python final).

Key Technical Innovations and Features

This project showcases several critical implementations necessary for production-grade agent systems:

A. Sequential Triage and Orchestration (orchestrate_task function)

The custom Python function, orchestrate_task, is the core of the state management. This tool is called repeatedly by the MTO to determine the next step:

Prioritization: It ensures tasks are processed in order of creation (lowest ID first).

Classification: It classifies tasks based on keywords in the description (e.g., 'buy', 'book', 'study'). This flexible keyword-based logic determines if a task requires delegation (requires_agent: true) or human intervention (requires_agent: false).

Result Guiding: It returns a structured JSON object that tells the MTO exactly what to do next (delegate, or prompt the user for manual completion).

B. Interactive Confirmation Loop

A crucial design decision was to enforce interactivity. The MTO's system instructions implement a strict confirmation loop:

Automatic Tasks: After a specialized agent successfully completes its action (e.g., provides flight options), the MTO asks: "Has this task (ID X) been fully completed? Reply Yes or No."

Manual Tasks: If the MTO detects a manual task (e.g., "Do a yoga session"), it prompts the user: "Task ID X is a manual task. Have you completed this yet? Reply Yes or No?"

This mechanism prevents the system from moving on prematurely and ensures the user always maintains explicit control over the workflow state. Upon receiving a "Yes," the MTO calls mark_complete(ID) and immediately executes orchestrate_task to proceed.

C. Real-Time Date and Time Awareness (A major fix implemented during development)

A key challenge faced and overcome was the agent's inability to correctly resolve relative dates ("next Thursday," "tomorrow").

Solution: We implemented a custom FunctionTool(get_current_date) that fetches the current system date.

Instruction Priority: The MTO's instructions were updated to include a critical directive: ALWAYS call get_current_date() first whenever any time-sensitive or relative date query is encountered. This ensures the LLM has the necessary context to make accurate calendar calculations before delegating to the Booking or Movie Agents.

D. Comprehensive To-Do List Utility

The project provides complete CRUD (Create, Read, Update, Delete) functionality for the task list state using dedicated utility tools:

add_task(description): Adds new items to the global list.

view_list(): Returns the current state, showing all PENDING and COMPLETE tasks.

mark_complete(ID): Updates a task status to completed (✅).

delete_task(ID): Fully removes tasks from the list, a critical feature added to clean up the workspace after completion.

Conclusion and Future Scope

The Automated To-Do List Orchestrator successfully demonstrates the power of the ADK in building sophisticated, stateful, and conversational applications. It moves beyond simple question-answering to provide a guided, interactive service.

The primary value lies in the secure and reliable sequential workflow, which is essential for any process-driven application. Future enhancements could include integrating a more advanced priority system (e.g., scheduling or urgency) and incorporating systems for visual confirmation of manual tasks.
