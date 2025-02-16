// DevShort---URL-Shortener
//DevShort is a lightweight URL shortener built using Node.js &amp; Express.js. It allows users to convert long URLs into short, easy-to-share links.
//its a javascript project\

// DevShort - सोपा URL शॉर्टनर 🚀

const express = require("express");
const fs = require("fs");
const crypto = require("crypto");

const app = express();
const PORT = 3000;
const DB_FILE = "urls.json";

// Middleware
app.use(express.json());

// जुने URLs लोड करणे
const loadURLs = () => {
    if (fs.existsSync(DB_FILE)) {
        return JSON.parse(fs.readFileSync(DB_FILE));
    }
    return {};
};

// URLs सेव करणे
const saveURLs = (data) => {
    fs.writeFileSync(DB_FILE, JSON.stringify(data, null, 2));
};

// शॉर्ट ID तयार करणे
const generateShortID = () => crypto.randomBytes(3).toString("hex");

const urls = loadURLs();

// URL शॉर्ट करण्यासाठी API
app.post("/shorten", (req, res) => {
    const { originalUrl } = req.body;
    if (!originalUrl) {
        return res.status(400).json({ error: "URL आवश्यक आहे" });
    }

    const shortID = generateShortID();
    urls[shortID] = originalUrl;
    saveURLs(urls);

    res.json({ shortUrl: `http://localhost:${PORT}/${shortID}` });
});

// शॉर्ट URL वर क्लिक केल्यावर ऑरिजिनल URL वर रीडायरेक्ट करणे
app.get("/:shortID", (req, res) => {
    const originalUrl = urls[req.params.shortID];
    if (originalUrl) {
        res.redirect(originalUrl);
    } else {
        res.status(404).json({ error: "URL सापडला नाही" });
    }
});

// सर्व्हर सुरु करणे
app.listen(PORT, () => console.log(`🚀 सर्व्हर सुरू झाला! http://localhost:${PORT}`));
