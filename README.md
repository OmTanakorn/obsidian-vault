# 🏰 The Arch Way: Obsidian Vault

> **Minimalist. Functional. Scalable.**
> A text-based productivity system designed for engineers. No bloat, just focus.

## 📂 Directory Structure (The DRI System)

We use numbered folders to keep everything sorted and logical:

*   **`00_Inbox/`**: Temporary dump for thoughts, drafts, or fleeting notes. Process this folder daily.
*   **`10_Journal/`**: Your life log.
    *   `Daily/`: Rapid logging and "One Big Thing" focus.
    *   `Weekly/`: Friday reviews and impact tracking (Source of Portfolio).
*   **`20_Projects/`**: Active work (e.g., Coding projects, Content creation).
*   **`30_Knowledge/`**: Your personal Wiki/Second Brain.
    *   `Tech/`: Languages, Frameworks, Tools (Go, Linux, Docker).
    *   `Concepts/`: System Design, Agile, Mental Models.
    *   `Snippets/`: Reusable code blocks.
*   **`90_Assets/`**: Images, attachments, and binary files.
*   **`99_System/`**: Engine room.
    *   `Templates/`: Source code for your note types.

---

## 🚀 Workflows

### 1. ☀️ Daily Routine (The Daily Log)
*   **Action**: Press `CMD/CTRL + P` -> "Open today's daily note".
*   **Focus**: Fill in **"One Big Thing"** (The single most important task).
*   **Log**: Use the "Log & Notes" section for rapid bullet journaling throughout the day.
*   **End**: Check the "End of Day Reflection" box before closing.

### 2. 📅 Weekly Review (Friday Ritual)
*   **Action**: Create a new note in `10_Journal/Weekly`.
*   **Template**: Insert `Weekly_Log`.
*   **Goal**: Review what you shipped. Rate your impact (1-5).
*   **Why**: This folder becomes your **Brag Document** for performance reviews or portfolio building.

### 3. 🛠 Starting a Project
*   **Action**: Create a new folder/note in `20_Projects`.
*   **Template**: Insert `Project_Readme`.
*   **Features**: Automatically tracks status (Active/On-hold) and stack (Go, HTMX, etc.).
*   **Dashboard**: Shows up automatically in `00_Home.md` if status is "active".

### 4. 🧠 Knowledge Management
*   **Action**: Create a new note in `30_Knowledge/Tech` (or relevant subfolder).
*   **Template**: Insert `Tech_Note`.
*   **Philosophy**: Write for your future self. Include code snippets and references.

---

## 🖥️ The Dashboard (`00_Home.md`)

Located at the root of your vault. It uses **Dataview** to dynamically display:
1.  **🔥 Active Projects**: What you are currently building.
2.  **📅 Recent Reviews**: Your productivity trend.
3.  **🧠 Latest Knowledge**: What you've learned recently.

---

## 🔌 Essential Plugins (Pre-installed)

We keep it light. Only the essentials:

1.  **Periodic Notes**: Manages Daily/Weekly note creation.
2.  **Templater**: Powering the logic in your templates (Date handling, Cursors).
3.  **Dataview**: The query engine for your Dashboard.
4.  **Smart Connections**: AI-powered linking (requires API Key).
5.  **Obsidian Git**: Auto-backup to this repository.

## ⚙️ Setup & Restoration

If you clone this repo to a new machine:

1.  Clone the repo:
    ```bash
    git clone git@github.com:OmTanakorn/obsidian-vault.git
    ```
2.  Open the folder in Obsidian.
3.  **Turn OFF Safe Mode** (to enable plugins).
4.  Run `git init` (if not already initialized).
5.  **Configure Smart Connections**: Enter your API Key in settings.

---

*System designed for the Arch Linux user mindset: "Keep it simple, stupid" (KISS).*
