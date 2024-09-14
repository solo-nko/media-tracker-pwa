# Media Tracker

## Overview

This is an application being developed to gain a better understanding of progressive web app development, component libraries, and indexedDB. I'm also making it because I've tried using OneNote and Excel to track my current media (what I'm currently playing, watching, reading etc.) and found it somewhat less than ideal. So, I'm attempting to make my own solution.

## Features

As this is a progressive app, it is installable to use locally! It uses the client-side database IndexedDB for its data storage.

## How to Use

```
# build for development:
npm run dev

# build for production:
npm run build

#run prettier formatter:
npm run format
```
See repository's prettier config (`.prettierrc.json`) for prettier rules.

## Tech Stack

This project was built with the following major technologies:

* Vue 3
* Vue Router 4
* Pinia
* Vite-PWA
* LuxonJs
* DexieJs
* QuillJs

## Things to investigate

* Export data to local file, and import data from local file
* Cloud sync (specifically between desktop and mobile)
  * Considering Firebase or Dexie Cloud for this
* Switch to MongoDB