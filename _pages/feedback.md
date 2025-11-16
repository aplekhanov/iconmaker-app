---
title: Feedback
include_in_header: false
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IconMaker Feedback</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: #f5f5f7;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }
        
        .container {
            background: white;
            padding: 40px;
            border-radius: 12px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            max-width: 500px;
            width: 100%;
        }
        
        .error-message {
            background: #fee;
            color: #c33;
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 20px;
            display: none;
            text-align: center;
        }
        
        h1 {
            color: #1d1d1f;
            margin-bottom: 10px;
            font-size: 28px;
        }
        
        .subtitle {
            color: #666;
            margin-bottom: 30px;
            font-size: 16px;
        }
        
        form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        
        label {
            color: #1d1d1f;
            font-weight: 500;
            font-size: 14px;
        }
        
        textarea {
            width: 100%;
            min-height: 150px;
            padding: 12px;
            border: 1px solid #d2d2d7;
            border-radius: 8px;
            font-size: 16px;
            font-family: inherit;
            resize: vertical;
            transition: border-color 0.2s;
        }
        
        textarea:focus {
            outline: none;
            border-color: #007aff;
        }
        
        .button-group {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }
        
        button {
            flex: 1;
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: 500;
            cursor: pointer;
            transition: opacity 0.2s;
        }
        
        .btn-submit {
            background: #007aff;
            color: white;
        }
        
        .btn-submit:hover {
            opacity: 0.9;
        }
        
        .btn-secondary {
            background: #e5e5ea;
            color: #1d1d1f;
        }
        
        .btn-secondary:hover {
            opacity: 0.8;
        }
        
        .success-message {
            display: none;
            text-align: center;
        }
        
        .success-message h2 {
            color: #1d1d1f;
            margin-bottom: 10px;
            font-size: 24px;
        }
        
        .success-message p {
            color: #666;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="error-message" id="errorMessage"></div>
        
        <div id="formContainer">
            <h1>We'd love your feedback! ❤️</h1>
            <p class="subtitle">Help us improve IconMaker by sharing your thoughts.</p>
            
            <form id="feedbackForm">
                <label for="feedback">Your Feedback</label>
                <textarea 
                    id="feedback" 
                    name="feedback" 
                    placeholder="What do you think about IconMaker? Any features you'd like to see?" 
                    required></textarea>
                
                <div class="button-group">
                    <button type="submit" class="btn-submit">Submit Feedback</button>
                    <button type="button" class="btn-secondary" onclick="dismiss()">Not Now</button>
                </div>
            </form>
        </div>
        
        <div class="success-message" id="successMessage">
            <h2>Thank you! 🎉</h2>
            <p>Your feedback means a lot to us.</p>
        </div>
    </div>

    <script>
        // Get URL parameters
        const urlParams = new URLSearchParams(window.location.search);
        const campaignId = urlParams.get('campaignId') || 'feedback_test';
        const isPlusUser = urlParams.get('isPlusUser') === 'true';
        const userId = urlParams.get('userId');
        
        // Hide form if not a Plus user
        if (!isPlusUser) {
            const errorDiv = document.getElementById('errorMessage');
            errorDiv.textContent = 'This feedback form is only available for Plus subscribers.';
            errorDiv.style.display = 'block';
            document.getElementById('formContainer').style.display = 'none';
        }
        
        // Send message to native app
        function sendToApp(message) {
            if (window.webkit?.messageHandlers?.iconMaker) {
                window.webkit.messageHandlers.iconMaker.postMessage(message);
            } else {
                console.log('Native bridge not available (testing in browser):', message);
            }
        }
        
        // Handle form submission
        document.getElementById('feedbackForm').addEventListener('submit', async (e) => {
            e.preventDefault();
            
            const formData = new FormData(e.target);
            const feedbackText = formData.get('feedback');
            
            // Show success message
            document.getElementById('formContainer').style.display = 'none';
            document.getElementById('successMessage').style.display = 'block';
            
            // Notify app that form was submitted
            sendToApp({
                type: 'formSubmitted',
                campaignId: campaignId,
                data: {
                    formId: 'feedback',
                    feedbackLength: feedbackText.length.toString()
                }
            });
            
            // Auto-dismiss after 2 seconds
            setTimeout(() => {
                sendToApp({
                    type: 'submitted',
                    campaignId: campaignId
                });
            }, 2000);
        });
        
        // Handle dismiss button
        function dismiss() {
            setTimeout(() => {
                sendToApp({
                    type: 'dismissed',
                    campaignId: campaignId
                });
            }, 1000);
        }
    </script>
</body>
</html>