require('dotenv').config();
const express = require('express');
const cors = require('cors');
const fetch = require('node-fetch');
const path = require('path');

const app = express();
const PORT = process.env.PORT || 3000;

app.use(cors());
app.use(express.json());
app.use(express.static(path.join(__dirname, 'public')));

app.post('/api/chat', async (req, res) => {
    const { message, model } = req.body;

    if (!message) {
        return res.status(400).json({ error: 'Message required' });
    }

    try {
        let responseText = '';

        if (model === 'groq') {
            const response = await fetch('https://api.groq.com/openai/v1/chat/completions', {
                method: 'POST',
                headers: {
                    'Authorization': `Bearer ${process.env.GROQ_API_KEY}`,
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    model: 'llama-3.3-70b-versatile',
                    messages: [{ role: 'user', content: message }]
                })
            });

            const data = await response.json();
            if (data.error) return res.status(400).json({ error: data.error.message });
            responseText = data.choices?.[0]?.message?.content || 'No response';
        } else {
            let modelName = 'gemini-3.6-flash';
            if (model === 'gemini-3.5') modelName = 'gemini-3.5-flash';
            if (model === 'gemini-lite') modelName = 'gemini-3.5-flash-lite';

            const response = await fetch(
                `https://generativelanguage.googleapis.com/v1beta/models/\( {modelName}:generateContent?key= \){process.env.GEMINI_API_KEY}`,
                {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{ parts: [{ text: message }] }]
                    })
                }
            );

            const data = await response.json();
            if (data.error) return res.status(400).json({ error: data.error.message });
            responseText = data.candidates?.[0]?.content?.parts?.[0]?.text || 'No response';
        }

        res.json({ reply: responseText });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});
