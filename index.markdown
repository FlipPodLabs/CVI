---
layout: default
---

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tavus AI Chat</title>
    <style>
        /* Add loading spinner CSS */
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

        /* Error message styling */
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
            // Show loading spinner
            loader.style.display = 'block';
            errorMessage.style.display = 'none';
            
            // API Call Configuration
            const response = await fetch('https://api.tavus.io/v1/conversations', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'x-api-key': '0fadf8d2ab914c91b1b0719eff16ea20' // Replace with your API key
                },
                body: JSON.stringify({
                    persona_id: 'r9602e00bd30' // Replace with your persona ID
                }),
                timeout: 10000 // 10-second timeout
            });

            // Handle HTTP errors
            if (!response.ok) {
                const errorText = await response.text();
                throw new Error(`API Error (${response.status}): ${errorText}`);
            }

            // Process successful response
            const data = await response.json();
            console.log('API Response:', data);

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
            console.error('Error Details:', error);
            errorMessage.textContent = `Error: ${error.message}`;
            errorMessage.style.display = 'block';
            
            // Handle specific error types
            if (error.name === 'TypeError' && error.message.includes('Failed to fetch')) {
                errorMessage.textContent = 'Network error: Please check your internet connection';
            }
        } finally {
            // Hide loading spinner
            loader.style.display = 'none';
        }
    });
    </script>
</body>
</html>
