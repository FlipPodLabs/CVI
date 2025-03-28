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
        script-src 'self' 'unsafe-eval' https://*.tavusapi.com https://*.filesusr.com;
        connect-src 'self' https://tavusapi.com;
        frame-src https://cvi.tavusapi.com;
        style-src 'self' 'unsafe-inline';
    ">
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
    <div class="loader"></div>
    <div class="error-message"></div>
    <div id="chatContainer" style="display:none;"></div>

    <!-- Include Daily.js for frame creation -->
    <script src="https://unpkg.com/@daily-co/daily-js"></script>
    <script>
      window.addEventListener('load', async () => {
          try {
              const response = await fetch('https://tavusapi.com/v2/conversations', {
                  method: 'POST',
                  headers: {
                      'Content-Type': 'application/json',
                      'x-api-key': 'YOUR_API_KEY' // Replace with your API key
                  },
                  body: JSON.stringify({
                      persona_id: 'YOUR_PERSONA_ID', // Replace with your Persona ID
                      conversational_context: "Default conversation starter",
                      properties: {
                          enable_recording: true
                      }
                  })
              });

              const data = await response.json();
              if (!data.conversation_url) {
                  throw new Error('Invalid API response format');
              }

              // Create and join the room using Daily.js
              const frame = window.Daily.createFrame();
              frame.join({ url: data.conversation_url });
              document.getElementById('chatContainer').appendChild(frame);
              document.getElementById('chatContainer').style.display = 'block';
          } catch (error) {
              console.error('Error:', error);
              alert('Failed to start conversation. Please try again.');
          }
      });
    </script>
</body>
</html>
