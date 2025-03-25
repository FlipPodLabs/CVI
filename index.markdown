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
    <meta http-equiv="Content-Security-Policy" content="
        default-src 'self';
        script-src 'self' 'unsafe-eval' 'sha256-4ZPsBzAj9gP0zFvccWBCEa3czx2vtHZiNMmXaNl8CTs=' https://*.tavus.io https://*.filesusr.com;
        connect-src 'self' https://api.tavus.io;
        frame-src https://cvi.tavus.io;
        style-src 'self' 'unsafe-inline';
    ">
    <style>
        /* Existing styles remain unchanged */
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
        const MAX_RETRIES = 3;
        let retryCount = 0;
        const controller = new AbortController();
        
        const executeCall = async () => {
            try {
                const response = await fetch('https://api.tavus.io/v2/conversations', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                        'x-api-key': '9836007c1c7e42069111b82b9fe6a6e4' // Your API key
                    },
                    body: JSON.stringify({
                        persona_id: 'p1fcd1b4f914', // Your persona ID
                        conversational_context: "Default conversation starter",
                        properties: {
                            enable_recording: true
                        }
                    }),
                    signal: controller.signal,
                    timeout: 30000 // 30-second timeout
                });

                // Handle API-specific errors
                if (!response.ok) {
                    const errorData = await response.json();
                    throw new Error(`Tavus API Error: ${errorData.error?.message || 'Unknown API error'}`);
                }

                const data = await response.json();
                
                // Validate response format
                if (!data.conversation_url) {
                    throw new Error('Invalid API response format');
                }

                // Success handling
                const iframe = document.createElement('iframe');
                iframe.src = data.conversation_url;
                iframe.style = 'width:100%;height:600px;border:none;';
                document.getElementById('chatContainer').appendChild(iframe);
                document.getElementById('chatContainer').style.display = 'block';

            } catch (error) {
                // Network/Timeout Errors
                if (error.name === 'AbortError' || error.message.includes('Failed to fetch')) {
                    if (retryCount < MAX_RETRIES) {
                        retryCount++;
                        await new Promise(resolve => setTimeout(resolve, 2000 * retryCount));
                        return executeCall();
                    }
                    throw new Error('Connection failed after 3 attempts. Check network or try later.');
                }
                
                // API Error Structure from Tavus docs
                if (error.message.includes('Tavus API Error')) {
                    console.error('API Failure:', error);
                    throw new Error('Service unavailable. Please contact support.');
                }
                
                // General error fallback
                throw error;
            }
        };

        try {
            await executeCall();
        } catch (error) {
            console.error('Final Error:', error);
            alert(error.message);
        } finally {
            controller.abort(); // Cleanup
        }
    });
    </script>
</body>
</html>
