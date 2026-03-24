# Travel Guide Generator

An n8n automation that generates personalized travel guide web pages based on user-submitted locations. Users submit their email and desired location through an Airtable form, and the system delivers a custom HTML travel guide with AI-generated attraction recommendations, images, and an interactive map.

## How It Works

1. **User submits a form** (Airtable) with their email and a location.
2. **Airtable Trigger** detects the new submission and kicks off the main workflow.
3. **Groq AI** generates attraction recommendations for the given location.
4. **Attractions are split** into 3 lists, and coordinates are fetched from OpenStreetMap.
5. **An HTML page** is assembled from a template, populated with attraction details, images, and a location map.
6. **The HTML is stored** in the Airtable row and served via a unique webhook URL.
7. **Gmail sends** the travel guide link to the user with Approve/Decline options.
8. If the user **declines**, a sub-workflow loop re-generates new recommendations and sends an updated page.

## Architecture

### Main Workflow
Airtable Trigger → Groq AI (generate attractions) → Extract & split attractions → Get coordinates (OpenStreetMap) → Merge data → Build HTML from template → Store in Airtable → Send email with Approve/Decline → Handle response → Optionally call sub-workflow loop

### Sub-Workflow Loop
Triggered on decline → Extract user note → Groq re-generates attractions based on feedback → Rebuild HTML page → Update Airtable record → Re-send email → Repeat until approved

### HTML Page Viewer
Webhook (unique URL per page) → Fetch HTML from Airtable by ID → Respond with the HTML content

### GitHub Backup
When any workflow finishes → HTTP requests pull workflow JSON from n8n API → Push/update files in GitHub repo

## Tech Stack

| Component | Purpose |
|---|---|
| **n8n** | Workflow automation platform |
| **Airtable** | Data storage, form submissions, HTML storage |
| **Groq AI** | LLM for generating attraction recommendations |
| **OpenStreetMap** | Geocoding (coordinates for map pins) |
| **Gmail** | Sending travel guide links + approval flow |
| **GitHub** | Workflow backup storage |

## Output 

Each generated travel guide page includes: <img align="right" width="300" alt="image" src="https://github.com/user-attachments/assets/6c1ff485-3b9d-4d05-8cd8-fab39d26e4a3" />

- Location images
- Top 3 attraction recommendations with descriptions
- An interactive location map
<br clear="right">
<br>

## Required API Keys / Credentials

- Airtable API key
- Groq API key
- Gmail OAuth credentials (client ID + secret)
- n8n API key (for backup workflow)
- GitHub personal access token

## Setup

1. Import the workflow JSON files into your n8n instance.
2. Configure all credentials listed above in n8n.
3. Create an Airtable base with columns: `Location`, `Email`, `Created Time`, `Links`, `HTML`.
4. Set up an Airtable form view with `Email` and `Location` fields.
5. Activate the workflows.
