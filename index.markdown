<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tt</title>
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
            margin: auto;
            display: none; /* Hidden by default */
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .error-message {
            color: red;
            margin-top: 10px;
            text-align: center;
        }

        #chatContainer {
            position: fixed; /* Center the chat screen */
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%); /* Adjust for centering */
            width: 80%; /* Larger width */
            height: 70%; /* Larger height */
            border-radius: 10px; /* Rounded corners for a clean look */
            box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.5); /* Subtle shadow for depth */
            overflow: hidden; /* Ensure clean edges */
        }
    </style>
</head>
<body>
    <h1>Welcome to CJI Chat</h1>
    <div class="loader"></div>
    <div class="error-message"></div>
    <div id="chatContainer"></div>

    <!-- Include Daily.js for frame creation -->
    <script src="https://unpkg.com/@daily-co/daily-js"></script>
    <script>
      window.addEventListener('load', async () => {
          try {
              const response = await fetch('https://tavusapi.com/v2/conversations', {
                  method: 'POST',
                  headers: {
                      'Content-Type': 'application/json',
                      'x-api-key': '9836007c1c7e42069111b82b9fe6a6e4' // Replace with your API key
                  },
                  body: JSON.stringify({
                      persona_id: 'p87c9456e1a9', // Replace with your Persona ID
                      conversational_context: "Default conversation starter",
                      properties: { enable_recording: true }
                  })
              });

              const data = await response.json();
              console.log("API Response:", data);

              if (!data.conversation_url) {
                  throw new Error('Invalid API response format');
              }

              // Create and join the room using Daily.js
              const frame = window.Daily.createFrame({
                  iframeStyle: { // Correct property name
                      position: "fixed",
                      top: "0",
                      left: "0",
                      width: "100%",
                      height: "100%",
                      borderRadius: "10px"
                  }
              });
              frame.join({ url: data.conversation_url });
              document.getElementById('chatContainer').appendChild(frame);
          } catch (error) {
              console.error('Error:', error);
              alert('Failed to start conversation. Please try again.');
          }
      });
    </script>
</body>
</html>
