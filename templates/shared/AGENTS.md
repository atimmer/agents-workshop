# Agents

This folder is the parent of several agents. Each subfolder is one agent, with its own instructions, memory and Git repository, and its own T3 Code project. This file is not versioned, so keep it short and don't store anything here that you'd want to keep.

## Working with another agent

Stay in your own folder and never edit another agent's files. When something belongs to another agent, hand it over in T3 Code:

1. Find that agent's project with `t3_project_list` (its `workspaceRoot` is the agent's folder). Load the T3 tools with your tool search if they're deferred.
2. Start a new thread there with `t3_thread_launch`, with a clear message that makes sense without this conversation.
3. Tell the user which thread you started. If the T3 tools aren't available, give the user a ready-to-paste prompt and ask them to start a new thread in that agent's project (right-click the project in the sidebar → **New thread**).
