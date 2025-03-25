---
layout: default
---

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tavus AI Chat</title>
    <link rel="stylesheet" href="{{ '/assets/main.css' | relative_url }}">
    <link rel="icon" href="{{ '/assets/images/favicon.ico' | relative_url }}">
    <style>
        .loader {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #3498db;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            animation: spin 1s linear infinite;
            margin: 20px auto;
            display: none;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .error-message {
            color: red;
            margin: 10px 0;
            display: none;
        }
    </style>
</head>
<body>
    <h1>Welcome to Tavus AI Chat</h1>
    <button id="startChat">Start AI Conversation</button>
    <div class="loader"></div>
    <div class="error-message"></div>
    <div id="chatContainer" style="display:none;"></div>

    <script>
    document.getElementById('startChat').addEventListener('click', async () => {
        const loader = document.querySelector('.loader');
        const errorMessage = document.querySelector('.error-message');
        
        try {
            loader.style.display = 'block';
            errorMessage.style.display = 'none';

            const controller = new AbortController();
            const timeoutId = setTimeout(() => controller.abort(), 8000);

            const response = await fetch('https://api.tavus.io/v1/conversations', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'x-api-key': '0fadf8d2ab914c91b1b0719eff16ea20' // Replace with your API key
                },
                body: JSON.stringify({
                    persona_id: 'p1fcd1b4f914' // Replace with your persona ID
                }),
                signal: controller.signal
            });

            clearTimeout(timeoutId);

            if (!response.ok) {
                const errorText = await response.text();
                throw new Error(`API Error (${response.status}): ${errorText}`);
            }

            const data = await response.json();

            if (data.conversation_url) {
                const iframe = document.createElement('iframe');
                iframe.src = data.conversation_url;
                iframe.style = 'width:100%;height:600px;border:none;';
                document.getElementById('chatContainer').appendChild(iframe);
                document.getElementById('chatContainer').style.display = 'block';
            } else {
                throw new Error('No conversation URL in response');
            }
        } catch (error) {
            console.error('Error:', error);
            errorMessage.textContent = `Error: ${error.message}`;
            errorMessage.style.display = 'block';

            if (error.name === 'AbortError') {
                errorMessage.textContent = 'Request timed out. Please try again.';
            } else if (error.name === 'TypeError' && error.message.includes('Failed to fetch')) {
                errorMessage.textContent = 'Network error. Please check your internet connection.';
            }
        } finally {
            loader.style.display = 'none';
        }
    });
    </script>
</body>
</html>
