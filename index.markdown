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
</head>
<body>
    <h1>Welcome to Tavus AI Chat</h1>
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
                      'x-api-key': '9836007c1c7e42069111b82b9fe6a6e4'
                  },
                  body: JSON.stringify({
                      persona_id: 'p1fcd1b4f914',
                      conversational_context: "Default conversation starter",
                      properties: { enable_recording: true }
                  })
              });

              const data = await response.json();
              console.log("API Response:", data);

              if (!data.conversation_url) {
                  throw new Error('Invalid API response format');
              }

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
