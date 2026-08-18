# Travel Guide Generator

An n8n automation project that generates personalized travel guide web pages. You fill in a location and your email, and a few moments later you get a custom HTML page with tourist attractions, AI-written recommendations, photos, and a map sent to your email.

## Disclaimer

This was a personal learning project built to explore n8n workflow automation and API integrations. It was made entirely with free-tier services, which introduced some limitations. For example, the email approval form times out after 15 minutes. The webhook URLs are also hardcoded to localhost since this was never deployed to production.

## About

The user fills out a simple form with their email and a travel destination:

<img width="300" alt="image" src="https://github.com/user-attachments/assets/b5c9fabe-2ad9-4032-a237-4e46ea961a75" />

Once submitted, the rest happens automatically: an AI generates attraction recommendations, images are pulled in, coordinates are geocoded, and a full HTML travel guide page is built and stored:

<img width="300" alt="image" src="https://github.com/user-attachments/assets/7f0fc8b2-3eb6-4400-be7f-5a90ec2525dd" />

The user gets an email with a link to their page and can either **approve** it or **decline** it with a note explaining what to change. If declined, the system regenerates new attractions based on the feedback and sends a new page. This loop repeats until the page is approved.

All submissions and generated pages are tracked in Airtable:

<img width="300" alt="image" src="https://github.com/user-attachments/assets/1c6eae5c-6d22-405b-90de-b42fea888c59" />

## The Workflows

The project is made up of 4 n8n workflows.

### Main Workflow

Handles the full pipeline from form submission to email delivery. Generates attractions and recommendations using Groq (Llama 3.3 70b), fetches images from Unsplash, geocodes locations with OpenStreetMap, builds a static map with Geoapify, assembles everything into an HTML page, and sends it via Gmail.

<img width="600" alt="image" src="https://github.com/user-attachments/assets/67468d69-62ae-48c2-96bc-8b820f490fe3" />

### Sub-Workflow Loop

Runs when the user declines a page. Takes their feedback, regenerates new attractions accordingly, rebuilds the page, and re-sends the email. Calls itself recursively on further declines.

<img width="600" alt="image" src="https://github.com/user-attachments/assets/91f3c4af-0678-4b4d-acc9-702dd040736e" />

### HTML Page Viewer

A simple webhook that serves the generated HTML pages. Each page gets a unique URL that fetches the HTML content from Airtable and displays it in the browser.

<img width="600" alt="image" src="https://github.com/user-attachments/assets/c783749d-118e-4bf0-82e7-3b23a41ff7fa" />

### GitHub Backup

Automatically backs up all workflow JSON files to GitHub whenever a user approves a page.

<img width="600" alt="image" src="https://github.com/user-attachments/assets/9b715f81-5a98-4751-8978-6f4c1bde5dc8" />

## Running It Yourself

To run this project you'll need an [n8n](https://n8n.io) instance. You can either sign up for n8n Cloud (paid service) or self-host it for free using Docker or npm.

Once you have n8n running, import the workflow JSON files: click the three dots menu in the top-right corner of n8n and select *Import from File*, then pick each JSON file.

After importing, you'll need to set up credentials in n8n for each service the workflows use. The workflows won't run until all credentials are configured. n8n will show you which nodes are missing credentials.

Necessary credentials:

- **Airtable** - create a free account at [airtable.com](https://airtable.com), set up a base with a `Submissions` table (columns: `Location`, `Email`, `Created Time`, `Links`, `HTML`), create a form view with `Email` and `Location` fields, and generate a Personal Access Token
- **Groq** - sign up at [groq.com](https://groq.com) and get a free API key
- **Unsplash** - register an app at [unsplash.com/developers](https://unsplash.com/developers) to get a Client ID
- **Geoapify** - sign up at [geoapify.com](https://www.geoapify.com/) for a free API key
- **Gmail** - the most involved one. You need to set up a project in [Google Cloud Console](https://console.cloud.google.com/), enable the Gmail API, configure an OAuth consent screen, and create OAuth2 credentials. Then authenticate through n8n's Gmail credential setup
- **GitHub** - generate a Personal Access Token at [github.com/settings/tokens](https://github.com/settings/tokens) and create a repo for the backups
- **n8n API key** - enable it in your n8n instance settings (needed for the backup workflow to export workflow JSONs)

Finally, update the Airtable base/table IDs in the workflow nodes to point to your own base, and update the webhook URLs if you're not running on `localhost:5678`. Then activate all 4 workflows.

## Built With

- **n8n** - workflow automation
- **Airtable** - form, database, and HTML storage
- **Groq** (Llama 3.3 70b) - AI-generated attractions and recommendations
- **Unsplash** - location images
- **OpenStreetMap** - geocoding
- **Geoapify** - static map with markers
- **Gmail** - email delivery and approval flow
- **GitHub** - workflow backups
