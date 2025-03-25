---
layout: default
---

<h1>Welcome to Tavus AI Chat</h1>
<button id="startChat">Start AI Conversation</button>
<div id="chatContainer" style="display:none;"></div>

<script>
document.getElementById('startChat').addEventListener('click', async () => {
    try {
        const response = await fetch('https://api.tavus.io/v1/conversations', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'x-api-key': '0fadf8d2ab914c91b1b0719eff16ea20'
            },
            body: JSON.stringify({
                persona_id: 'r9602e00bd30'
            })
        });

        const data = await response.json();
        if (data.conversation_url) {
            const iframe = document.createElement('iframe');
            iframe.src = data.conversation_url;
            iframe.style = 'width:100%;height:600px;border:none;';
            document.getElementById('chatContainer').appendChild(iframe);
            document.getElementById('chatContainer').style.display = 'block';
        }
    } catch (error) {
        console.error('Error:', error);
        alert('Failed to start conversation. Please try again.');
    }
});
</script>