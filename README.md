# dashboard
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Company Dashboard</title>
    <script src="https://cdn.plot.ly/plotly-2.32.0.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            background: linear-gradient(135deg, rgba(173, 216, 230, 0.7), rgba(240, 255, 255, 0.8));
            backdrop-filter: blur(10px);
            padding: 20px;
        }
        #dashboard {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            justify-content: center;
        }
        .chart-container {
            width: 40%;
            min-width: 400px;
            background: rgba(255, 255, 255, 0.2);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        input, .download-btn {
            margin-bottom: 20px;
            padding: 10px;
            border: none;
            border-radius: 10px;
            background: rgba(255, 255, 255, 0.3);
            backdrop-filter: blur(5px);
            cursor: pointer;
            transition: 0.3s;
        }
        input:hover, .download-btn:hover {
            background: rgba(255, 255, 255, 0.5);
        }
    </style>
</head>
<body>
    <h2>Company Dashboard</h2>
    <input type="file" id="fileInput" accept=".json">
    <button class="download-btn" onclick="downloadFile()">Download HTML</button>
    <div id="dashboard">
        <div class="chart-container" id="genderChart"></div>
        <div class="chart-container" id="emailDomainChart"></div>
        <div class="chart-container" id="geoMap"></div>
        <div class="chart-container" id="ipHistogram"></div>
    </div>
    
    <script>
        document.getElementById('fileInput').addEventListener('change', function(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const data = JSON.parse(e.target.result);
                    generateDashboard(data);
                };
                reader.readAsText(file);
            }
        });
        
        function generateDashboard(data) {
            const genders = data.map(d => d.gender);
            const emailDomains = data.map(d => d.email.split('@')[1]);
            const ipAddresses = data.map(d => d.ip_address.split('.')[0]);
            
            // Gender Distribution Chart
            const genderCounts = countOccurrences(genders);
            Plotly.newPlot('genderChart', [{
                type: 'pie', labels: Object.keys(genderCounts), values: Object.values(genderCounts)
            }], { title: 'Gender Distribution' });
            
            // Email Domain Chart
            const emailCounts = countOccurrences(emailDomains);
            Plotly.newPlot('emailDomainChart', [{
                type: 'bar', x: Object.keys(emailCounts), y: Object.values(emailCounts)
            }], { title: 'Email Domains', xaxis: { title: 'Domain' }, yaxis: { title: 'Count' } });
            
            // Geo Map (Simulated Locations)
            const locations = data.map(d => ({ lat: Math.random() * 180 - 90, lon: Math.random() * 360 - 180 }));
            Plotly.newPlot('geoMap', [{
                type: 'scattergeo', mode: 'markers', 
                lat: locations.map(l => l.lat), lon: locations.map(l => l.lon),
                marker: { size: 8, color: 'blue' }
            }], { title: 'Geolocation of Sales Reps', geo: { showland: true } });
            
            // IP Address Histogram
            const ipCounts = countOccurrences(ipAddresses);
            Plotly.newPlot('ipHistogram', [{
                type: 'bar', x: Object.keys(ipCounts), y: Object.values(ipCounts)
            }], { title: 'IP Address Ranges', xaxis: { title: 'First Octet' }, yaxis: { title: 'Count' } });
        }
        
        function countOccurrences(arr) {
            return arr.reduce((acc, val) => {
                acc[val] = (acc[val] || 0) + 1;
                return acc;
            }, {});
        }
        
        function downloadFile() {
            const element = document.createElement('a');
            const fileContent = document.documentElement.outerHTML;
            const blob = new Blob([fileContent], { type: 'text/html' });
            element.href = URL.createObjectURL(blob);
            element.download = 'company_dashboard.html';
            document.body.appendChild(element);
            element.click();
            document.body.removeChild(element);
        }
    </script>
</body>
</html>
