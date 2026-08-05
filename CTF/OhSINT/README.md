\# OhSINT - TryHackMe

**Room:** https://tryhackme.com/room/ohsint  
**Difficulty:** Easy  
**Category:** CTF / OSINT  
**Status:** ✅ Completed  
**Date:** Aug 2026

---

## Overview
OhSINT is a beginner-friendly CTF room focused on Open Source Intelligence (OSINT). The challenge requires analyzing an image file and correlating publicly available information to build a profile on the target "OWoodflint" and answer a series of questions.

## Tools & Techniques Used

| Tool | Command / Usage | Purpose |
| --- | --- | --- |
| **ExifTool** | `exiftool image.jpg` | Extracted metadata: GPS coordinates, BSSID, timestamps, camera details. |
| **Google** | `site:twitter.com "OWoodflint"` | Used advanced search operators to locate social media profiles. |
| **Reverse Image Search** | Google Images / Yandex | Found other instances of the provided image online. |
| **Wigle.net** | BSSID lookup | Converted wireless BSSID from EXIF data to physical location. |
| **GitHub Search** | Username search | Located potential GitHub profile associated with the target. |
| **Metadata2Go.com** | Online EXIF viewer | Alternative method for quick metadata extraction. |

## Methodology
1.  **Initial Recon:** Downloaded and inspected the provided image for any visual clues.
2.  **Metadata Extraction:** Used ExifTool to parse the image EXIF data, which revealed critical clues including GPS coordinates and a WiFi BSSID.
3.  **Geolocation:** Leveraged Wigle.net and Google Maps to plot the GPS coordinates and identify the location.
4.  **OSINT Pivoting:** Used the discovered username "OWoodflint" to search across multiple platforms including Twitter and GitHub.
5.  **Data Correlation:** Combined all findings from metadata, geolocation, and social media to answer all room questions.

## What I Learned
1.  **Metadata is crucial:** A single image can leak GPS location, device info, and network details. Always sanitize images before sharing.
2.  **OSINT is about chaining:** One piece of data leads to another. BSSID → Location → Social Media → Full Profile.
3.  **Tool selection matters:** Knowing when to use ExifTool vs online viewers, or Google vs Yandex for reverse image search, saves time.
4.  **OPSEC awareness:** This room demonstrates how easy it is to dox someone from a simple photo. Critical for red team and blue team awareness.

## Skills Gained
- EXIF metadata extraction and analysis
- Geolocation using GPS and BSSID data
- Username enumeration across platforms
- Correlating fragmented OSINT data points
- CTF methodology and documentation

## Screenshot
![OhSINT Completion](./images/ohsint-completed.png)

---
**Related Rooms:** Sakura Room, Google Dorking, Intro to OSINT  
**Next Steps:** Learn more about Maltego and SpiderFoot for automated OSINT
