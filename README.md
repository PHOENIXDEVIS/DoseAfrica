from http.server import BaseHTTPRequestHandler, HTTPServer
from urllib.parse import urlparse, parse_qs
import webbrowser
import threading

# Fixed: Wrapped strings in quotes and updated port variable name to uppercase
HOST = "localhost"
PORT = 8000

# Complete HTML template with closed tags and functional JavaScript search logic
HTML_CONTENT = r"""<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PharmaLearn | Pharmacology Education</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; }
        html { scroll-behavior: smooth; }
        body { font-family: Arial, Helvetica, sans-serif; background: #f4f8fb; color: #222; line-height: 1.6; }
        
        /* NAVIGATION */
        header { background: #063970; color: white; padding: 15px 7%; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 1000; }
        .logo { font-size: 24px; font-weight: bold; }
        nav { display: flex; gap: 20px; }
        nav a { color: white; text-decoration: none; font-weight: bold; }
        nav a:hover { color: #9ddcff; }
        
        /* HERO */
        .hero { min-height: 500px; display: flex; align-items: center; padding: 70px 10%; color: white; background: linear-gradient(rgba(6, 57, 112, 0.90), rgba(6, 57, 112, 0.90)), url('https://images.unsplash.com/photo-1585435557343-3b092031a831') center/cover; }
        .hero-content { max-width: 750px; }
        .hero h1 { font-size: 50px; margin-bottom: 20px; }
        .hero p { font-size: 20px; margin-bottom: 30px; }
        .btn { display: inline-block; padding: 13px 25px; background: white; color: #063970; text-decoration: none; border-radius: 6px; font-weight: bold; }
        .btn:hover { background: #dff3ff; }
        
        /* SEARCH */
        .search-section { background: white; padding: 50px 10%; text-align: center; }
        .search-section h2 { color: #063970; margin-bottom: 20px; }
        #searchBox { width: 90%; max-width: 700px; padding: 15px; border: 2px solid #063970; border-radius: 6px; font-size: 16px; outline: none; }
        
        /* CONTENT SECTION */
        .content { padding: 50px 10%; display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 30px; }
        .card { background: white; border-radius: 8px; padding: 25px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); border-top: 5px solid #063970; }
        .card h3 { color: #063970; margin-bottom: 10px; }
    </style>
</head>
<body>

    <header>
        <div class="logo">PharmaLearn</div>
        <nav>
            <a href="#">Home</a>
            <a href="#search">Search</a>
            <a href="#modules">Modules</a>
        </nav>
    </header>

    <section class="hero">
        <div class="hero-content">
            <h1>Master Pharmacology</h1>
            <p>Your interactive educational resource for drug classifications, mechanisms of action, and clinical therapeutics.</p>
            <a href="#search" class="btn">Get Started</a>
        </div>
    </section>

    <section id="search" class="search-section">
        <h2>Search Drug Databases</h2>
        <input type="text" id="searchBox" placeholder="Type a drug name, category, or mechanism (e.g., Beta-blocker)..." onkeyup="filterCards()">
    </section>

    <section id="modules" class="content">
        <div class="card">
            <h3>Cardiovascular Drugs</h3>
            <p>Explore Beta-blockers, ACE inhibitors, and Calcium Channel Blockers management.</p>
        </div>
        <div class="card">
            <h3>Antibiotics</h3>
            <p>Understand cell wall synthesis inhibitors, protein synthesis inhibitors, and resistance.</p>
        </div>
        <div class="card">
            <h3>CNS Agents</h3>
            <p>Study neurotransmitters, antidepressants, antipsychotics, and anxiolytics.</p>
        </div>
    </section>

    <script>
        function filterCards() {
            let input = document.getElementById('searchBox').value.toLowerCase();
            let cards = document.getElementsByClassName('card');
            
            for (let i = 0; i < cards.length; i++) {
                let txtValue = cards[i].textContent || cards[i].innerText;
                if (txtValue.toLowerCase().indexOf(input) > -1) {
                    cards[i].style.display = "";
                } else {
                    cards[i].style.display = "none";
                }
            }
        }
    </script>
</body>
</html>
"""

class PharmaRequestHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        # Serve the page layout
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        self.wfile.write(bytes(HTML_CONTENT, "utf-8"))

def open_browser():
    # Helper to open the browser window automatically once the port goes live
    webbrowser.open(f"http://{HOST}:{PORT}")

if __name__ == "__main__":
    server = HTTPServer((HOST, PORT), PharmaRequestHandler)
    print(f"Server started at http://{HOST}:{PORT}")
    
    # Run browser launch in a separate thread so it doesn't block the server loop
    threading.Thread(target=open_browser).start()
    
    try:
        server.serve_forever()
    except KeyboardInterrupt:
        print("\nShutting down server.")
        server.server_close()
