# readeck-to-karakeep

A small python script for importing a Readeck backup into Karakeep. I wrote out these detailed steps in case a low/no-code self-hoster (like me!) is trying to make a similar migration from Readeck to Karakeep.

### Use Case

Import Readeck content into Karakeep while maintaining bookmark tags & archive status.

### Background

About 1.5 years ago I started using [Readeck](https://readeck.org/en/) as a self-hosted replacement for Pocket, which I had been using for over a decade. Readeck is incredible if you're looking for a self-hosted bookmarking or read later application, and I was able to import my Pocket data to Readeck with no problems.

However, as of 2025, Readeck currently doesn't have the sweet, sweet AI features that [Karakeep](https://karakeep.app/) has, and I want that magic self-hosted AI summary & AI auto-tagging. Also, seeing those Karakeep screenshots on reddit were really starting to grown on me. 

All that context - I vibe-coded this python script together using generative AI, and it works. I have succesfully imported my Readeck bookmarks into Karakeep.

### **How it works**

The script uses Karakeep’s API access to work, so you will need to create an API Key in Karakeep (very easy to do).

## Steps:

1. Created a Readeck backup using [the export method defined in the Readeck documentation](https://readeck.org/en/docs/backups).
    
    > To perform a full export, simply use the following command:
    > 
    > 
    > ```bash
    > readeck export -config /path/to/config.toml export.zip
    > ```
    > 
2. Unzip the Readeck export archive locally on your machine
3. In Karakeep - create an API Key in **User Settings > API Keys**
4. Run the python script in terminal. It will prompt for the Karakeep API endpoint (e.g. *https://karakeep.example.com/api/v1*), API key, and location of the Readeck export folder. 

My archive with about ~1000 bookmarks and ~150 tags took about 20-30 minutes, I imagine mileage may vary.
