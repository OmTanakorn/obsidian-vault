# 🏰 GEMINI.md: The Arch Way Obsidian Vault

This directory is an Obsidian Vault designed for software engineers, following the **DRI System** (Numbered Folders for Focus). It prioritizes a minimalist, functional, and scalable structure for personal knowledge management, task tracking, and professional journaling.

## 📂 Directory Overview (DRI System)

- **`00_Inbox/`**: Temporary staging area for fleeting notes and raw information.
- **`10_Journal/`**: Chronological logs.
    - `Daily/`: Daily rapid logging and "One Big Thing" focus.
    - `Weekly/`: Friday reflections used for impact tracking and portfolio building.
- **`20_Projects/`**: Active development projects and initiatives.
- **`30_Knowledge/`**: The "Second Brain."
    - `Tech/`: Technical documentation on languages (Go, Python), tools (Docker, Postgres), and platforms (Linux).
    - `Concepts/`: High-level principles like Clean Architecture and System Design.
    - `Snippets/`: Reusable code blocks.
- **`90_Assets/`**: Static files, images, and binary attachments.
- **`99_System/`**: The vault's engine, containing templates and system-wide configurations.

## 🔑 Key Files

- **`00_Home.md`**: The central dashboard. It uses **Dataview** queries to dynamically aggregate active projects, recent weekly reviews, and the latest knowledge entries.
- **`README.md`**: Comprehensive documentation of the vault's philosophy, setup, and workflows.
- **`99_System/Templates/`**:
    - `Daily_Log.md`: Standard structure for daily notes with "One Big Thing" and reflection prompts.
    - `Tech_Note.md`: Template for technical documentation with metadata for auto-discovery.
    - `Project_Readme.md`: Template for initializing new project trackers.

## 🚀 Workflows & Conventions

### Metadata (Frontmatter)
Notes rely on YAML frontmatter for categorization and dashboard integration. Key fields include:
- `type`: (e.g., `daily`, `weekly`, `knowledge`, `project`)
- `status`: Used in `20_Projects` (e.g., `active`, `on-hold`)
- `publish`: Boolean to control visibility in the `30_Knowledge` dashboard.
- `stack`: Technology stack used in projects.

### Daily & Weekly Rituals
1. **Daily**: Focus on the "One Big Thing." End-of-day reflections include clearing the `00_Inbox` and updating project statuses.
2. **Weekly**: Impact scoring (1-5) to track productivity and progress for professional reviews.

### Knowledge Capture
Write for your "future self." Technical notes should include concepts, working code examples, and references.

## 🛠️ Technical Stack (Plugins)

- **Templater**: Handles dynamic date logic and metadata injection.
- **Dataview**: Powers the dynamic dashboards in `00_Home.md`.
- **Periodic Notes**: Automates the creation of Daily and Weekly logs.
- **Smart Connections**: AI-powered discovery of related notes.
- **Obsidian Git**: Version control and automated backups.
- **Excalidraw**: For architectural diagrams and visual thinking.

## 🤖 Interaction Guidelines for Gemini

- **Note Creation**: When creating new notes, always use the relevant template structure from `99_System/Templates/` and include appropriate YAML frontmatter.
- **Linking**: Favor internal Wiki-links `[[Note Name]]` to maintain the graph structure.
- **Queries**: You can use Dataview-like logic to help the user find or summarize information across folders.
- **Context**: If asked about a project or technology, check `20_Projects/` and `30_Knowledge/` respectively for existing context.
