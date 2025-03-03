# Audio-two-video
Audio File import injoy next magic
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Audio-to-Video Converter | AI Powered</title>
    <style>
        /* Reset and Base Styles */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1a1a1a, #2d2d2d);
            color: #fff;
            min-height: 100vh;
        }

        /* Header Styles */
        .header {
            background: rgba(0, 0, 0, 0.8);
            padding: 1rem 2rem;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }

        .nav-menu {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .logo {
            font-size: 1.8rem;
            font-weight: bold;
            background: linear-gradient(45deg, #00ff88, #00b4d8);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        /* Main Content Styles */
        .container {
            max-width: 800px;
            margin: 3rem auto;
            padding: 2rem;
            text-align: center;
        }

        .upload-section {
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            padding: 2rem;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        .file-input {
            margin: 2rem 0;
        }

        input[type="file"] {
            display: none;
        }

        .custom-upload {
            background: #00b4d8;
            color: white;
            padding: 1rem 2rem;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .custom-upload:hover {
            background: #00ff88;
            transform: translateY(-2px);
        }

        /* Results Section */
        #results {
            margin-top: 2rem;
            display: none;
        }

        /* Footer Styles */
        .footer {
            background: rgba(0, 0, 0, 0.8);
            padding: 2rem;
            text-align: center;
            position: fixed;
            bottom: 0;
            width: 100%;
        }

        .footer-content {
            max-width: 800px;
            margin: 0 auto;
        }

        /* Loading Animation */
        .loader {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #00b4d8;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 2rem auto;
            display: none;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <!-- Header -->
    <header class="header">
        <nav class="nav-menu">
            <div class="logo">Audio2Video AI</div>
        </nav>
    </header>

    <!-- Main Content -->
    <div class="container">
        <div class="upload-section">
            <h2>Convert Audio to Video</h2>
        <p>Upload your audio file (MP3, WAV) to generate video</p>
            
            <div class="file-input">
                <input type="file" id="audioFile" accept=".mp3,.wav">
                <label for="audioFile" class="custom-upload">Choose Audio File</label>
            </div>
            
            <div id="results"></div>
            <div class="loader" id="loader"></div>
        </div>
    </div>

    <!-- Footer -->
    <footer class="footer">
        <div class="footer-content">
            <p>© 2024 Audio2Video AI. All rights reserved.</p>
        </div>
    </footer>

    <script>
        // Get Hugging Face Token: https://huggingface.co/settings/tokens
        const HF_API_TOKEN = 'YOUR_HUGGINGFACE_TOKEN';
        const API_URL = 'https://api-inference.huggingface.co/models/facebook/wav2vec2-base-960h';

        const audioInput = document.getElementById('audioFile');
        const resultsDiv = document.getElementById('results');
        const loader = document.getElementById('loader');

        audioInput.addEventListener('change', async (e) => {
            const file = e.target.files[0];
            if (!file) return;

            try {
                showLoading(true);
                const formData = new FormData();
                formData.append('file', file);

                // Send audio to Hugging Face API
                const response = await fetch(API_URL, {
                    headers: {
                        'Authorization': `Bearer ${HF_API_TOKEN}`
                    },
                    method: 'POST',
                    body: formData
                });

                const result = await response.json();
                
                if (result.error) {
                    showError(result.error);
                    return;
                }

                // Process and display video
                displayVideo(result);
            } catch (error) {
                showError(error.message);
            } finally {
                showLoading(false);
            }
        });

        function displayVideo(videoData) {
            resultsDiv.style.display = 'block';
            // Modify this based on your model's output format
            resultsDiv.innerHTML = `
                <h3>Generated Video:</h3>
                <video controls style="width: 100%; margin-top: 1rem;">
                    <source src="data:video/mp4;base64,${videoData}" type="video/mp4">
                    Your browser does not support the video tag.
                </video>
            `;
        }

        function showLoading(show) {
            loader.style.display = show ? 'block' : 'none';
        }

        function showError(message) {
            resultsDiv.innerHTML = `<p class="error">Error: ${message}</p>`;
            resultsDiv.style.display = 'block';
        }
    </script>
</body>
</html>
