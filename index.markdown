 <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>More Ideas</title>
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
        body {
            font-family: 'Roboto', sans-serif;
            margin: 0;
            padding: 0;
            background-color: #101010; /* Futuristic dark background */
            color: #ffffff; /* Clean white text */
        }

        h1 {
            text-align: center;
            font-size: 2.5rem;
            margin-top: 20px;
            color: #00d1ff; /* Bright blue accent for futuristic feel */
        }

        .chat-container {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh; /* Full-screen layout */
        }

        #chatContainer {
            width: 90%; /* Large popup size */
            height: 80%; /* Large popup size */
            border-radius: 10px;
            overflow: hidden; /* Clean edges */
            box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.5); /* Subtle shadow */
        }
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
    <h1>Welcome to CJI</h1>
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
                      'x-api-key': '9836007c1c7e42069111b82b9fe6a6e4' // Replace with your API key
                  },
                  body: JSON.stringify({
                      persona_id: 'p1fcd1b4f914', // Replace with your Persona ID
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
