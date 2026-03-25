<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jarvis Final Offline Fix</title>
    <script>
        // এই ফাংশনটি লাইব্রেরি লোড না হওয়া পর্যন্ত বাটন অফ রাখবে
        function checkLib() {
            if (window.LiveKit) {
                document.getElementById('btn').disabled = false;
                document.getElementById('btn').innerText = "ACTIVATE JARVIS";
                document.getElementById('status').innerText = "সিস্টেম প্রস্তুত! টোকেন দিন।";
            }
        }
    </script>
    <script src="https://unpkg.com/livekit-client@1.15.4/dist/livekit-client.umd.min.js" onload="checkLib()"></script>
    
    <style>
        body { background: #050a10; color: #00f2ff; font-family: sans-serif; text-align: center; display: flex; align-items: center; justify-content: center; height: 100vh; margin: 0; }
        .box { background: #0a111a; padding: 30px; border-radius: 15px; border: 2px solid #00f2ff; width: 90%; max-width: 380px; box-shadow: 0 0 20px rgba(0,242,255,0.2); }
        input { width: 100%; padding: 12px; margin: 15px 0; background: #000; border: 1px solid #00f2ff; color: #fff; border-radius: 6px; box-sizing: border-box; }
        button { width: 100%; padding: 15px; background: #00f2ff; color: #000; border: none; border-radius: 6px; font-weight: bold; font-size: 16px; cursor: pointer; transition: 0.3s; }
        button:disabled { background: #333; color: #666; cursor: not-allowed; }
        #status { margin-top: 20px; font-size: 13px; color: #ffae00; font-weight: bold; }
    </style>
</head>
<body>

    <div class="box">
        <h2 style="margin:0; letter-spacing: 2px;">JARVIS UI</h2>
        <p style="font-size: 10px; color: #444;">ID: iaapchbm</p>
        
        <input type="text" id="tokenInput" placeholder="এখানে Access Token পেস্ট করুন">
        
        <button id="btn" onclick="startJarvis()" disabled>লাইব্রেরি চেক হচ্ছে...</button>
        
        <div id="status">লাইব্রেরি ডাউনলোড হচ্ছে, দয়া করে ৫ সেকেন্ড অপেক্ষা করুন...</div>
    </div>

    <script>
        // যদি ওপরের লিঙ্ক কাজ না করে, তবে এটি বিকল্প হিসেবে কাজ করবে
        setTimeout(() => {
            if (!window.LiveKit) {
                document.getElementById('status').innerText = "এরর: আপনার ব্রাউজার ফাইল ব্লক করছে। দয়া করে Chrome-এ চেষ্টা করুন।";
            }
        }, 10000);

        async function startJarvis() {
            const token = document.getElementById('tokenInput').value;
            const status = document.getElementById('status');
            const url = "wss://jarvis-voiceassistant-iaapchbm.livekit.cloud";

            if (!token) {
                alert("টোকেন না দিলে চালু হবে না!");
                return;
            }

            try {
                status.innerText = "কানেক্ট করা হচ্ছে... দয়া করে অপেক্ষা করুন";
                const room = new LiveKit.Room();

                room.on(LiveKit.RoomEvent.TrackSubscribed, (track) => {
                    if (track.kind === 'audio') {
                        const audioEl = track.attach();
                        document.body.appendChild(audioEl);
                    }
                });

                await room.connect(url, token);
                
                status.innerText = "সফল! Jarvis আপনার কথা শুনছে।";
                status.style.color = "#00ff00";
                document.getElementById('btn').innerText = "JARVIS ACTIVE";
                
                // মাইক্রোফোন চালু
                await room.localParticipant.setMicrophoneEnabled(true);

            } catch (e) {
                console.error(e);
                status.innerText = "ব্যর্থ: " + e.message;
                status.style.color = "#ff4444";
            }
        }
    </script>
</body>
</html>
