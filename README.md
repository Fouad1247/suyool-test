<html>
<head>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.2.0/crypto-js.min.js"></script>
    <script>
        function generateSecureHash(data, certificate) {
            const concatenatedData = 
                data.tranID +
                data.merchantAccountID +
                data.amount +
                data.currency +
                data.CallBackURL +
                data.tranTS +
                data.additionalInfo +
                certificate;

            return CryptoJS.SHA512(concatenatedData).toString(CryptoJS.enc.Base64);
        }

        function createSuyoolPaymentIframe(data, certificate) {
            const secureHash = generateSecureHash(data, certificate);

            const encodedCallBackURL = encodeURIComponent(data.CallBackURL);
            const encodedBrowserType = encodeURIComponent(data.browserType);
            const encodedSecureHash = encodeURIComponent(secureHash);
            const baseUrl = 'https://sandbox.suyool.com/paysuyoolmobile/?';

            const fullUrl = baseUrl +
                `AdditionalInfo=${encodeURIComponent(data.additionalInfo)}` +
                `&TranID=${data.tranID}` +
                `&Amount=${data.amount}` +
                `&Currency=${data.currency}` +
                `&TranTS=${data.tranTS}` +
                `&CallBackURL=${encodedCallBackURL}` +
                `&browserType=${encodedBrowserType}` +
                `&SecureHash=${encodedSecureHash}` +
                `&TS=${data.ts}` +
                `&MerchantID=${data.merchantAccountID}` +
                `&CurrentUrl=${data.currentUrl}`;

            console.log("Full iFrame URL:", fullUrl);

            const iframe = document.createElement('iframe');
            iframe.src = fullUrl;
            iframe.width = '100%';
            iframe.height = '1200px';
            document.body.appendChild(iframe);
        }

        function handleFormSubmit(event) {
            event.preventDefault();

            const amount = parseFloat(document.getElementById('amount').value).toFixed(3);
            const timestamp = Math.floor(Date.now() / 1000); // Unix timestamp in seconds
            const data = {
                amount: amount,
                currency: document.getElementById('currency').value,
                tranID: "tran_" + Math.random().toString(36).substr(2, 9) + "_" + Date.now(),
                tranTS: timestamp.toString(),
                ts: timestamp.toString(),
                merchantAccountID: '50',
                CallBackURL: 'https://www.youtube.com/',
                additionalInfo: 'fouad_testingpayment',
                currentUrl: 'https://suyool.com',
                browserType: 'Chrome125'
            };
            const certificate = 'eSW32UuxujH9dzww1w0uNy9WNgmLneV4Da9a6HZNUawDhSQXW2RDaxLGuzKxPxGWSvNcFSBK6LpPN1m5CuejLJqjVLeQibpzacKe';

            createSuyoolPaymentIframe(data, certificate);
        }
    </script>
</head>
<body>
    <h1>Suyool Payment Integration Test</h1>
    <form id="paymentForm" onsubmit="handleFormSubmit(event)">
        <div>
            <label for="amount">Amount:</label>
            <input type="number" id="amount" name="amount" step="0.01" required>
        </div>
        <div>
            <label for="currency">Currency:</label>
            <select id="currency" name="currency" required>
                <option value="USD">USD</option>
                <option value="LBP">LBP</option>
            </select>
        </div>
        <button type="submit">Pay with Suyool</button>
    </form>
</body>
</html>
