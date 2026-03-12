# copilot-agents

A collection of AI agent definitions, skill definitions, and related resources for use with AI assistants such as GitHub Copilot, Claude, and similar tools.

## Repository Structure

```
agents/
  generic/          # General-purpose agents, not tied to any specific language
  languages/        # Language-specific agents organised by programming language
    python/
    javascript/
    java/
    csharp/
  sets/             # Named groups of agents designed to be used together
skills/             # Reusable skill definitions that agents can reference
```

## Agent Definitions

Each agent is defined in a YAML file with a consistent schema:

```yaml
name: <agent-name>
version: <semver>
description: <short description>
category: generic | language | set-member
language: <optional – for language-specific agents>
skills:
  - <skill-name>
model: <suggested model>
instructions: |
  <detailed instructions for the agent>
```

## Skill Definitions

Each skill is defined in a YAML file:

```yaml
name: <skill-name>
version: <semver>
description: <short description>
parameters:
  - name: <parameter-name>
    description: <what the parameter controls>
    required: true | false
    default: <optional default value>
instructions: |
  <detailed instructions for applying this skill>
```

## Agent Categories

| Category | Description |
|---|---|
| **Generic** | General-purpose agents applicable to any codebase or project |
| **Language-specific** | Agents with deep knowledge of a particular programming language |
| **Sets** | Curated combinations of agents designed to work together on a common goal |

## Usage

Agent and skill definition files are plain YAML. Load them into your AI assistant workflow, MCP server, or orchestration layer of choice, passing the `instructions` field as the system prompt and wiring up the referenced `skills`.
