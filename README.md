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
             CARDIOVASCULAR DRUGS
Dosage • Prescription • Administration • Storage • Expiry
An educational quick-reference for pharmacy and healthcare students

Scope	Common adult cardiovascular medicines used for hypertension, angina, heart failure, thrombosis and dyslipidaemia.
Important	Doses below are typical adult examples, not individual prescriptions. Actual treatment depends on diagnosis, BP/HR, renal/hepatic function, age, interactions and local guidelines.
Safety	Prescription cardiovascular medicines should be supplied and monitored by an authorised healthcare professional.

Clinical foundation
WHO identifies major cardiovascular medicines such as diuretics, ACE inhibitors, beta-blockers, calcium-channel blockers and statins among basic medicines used in cardiovascular care. Treatment selection should follow clinical assessment and applicable national/WHO guidance.
 
1. Key Principles of Cardiovascular Medicines
•	Do not start, stop, double or change a cardiovascular medicine without professional advice. Some medicines can cause dangerous rebound effects or deterioration when stopped suddenly.
•	Prescription status varies by country and product. In Uganda, dispensing should follow applicable national medicines regulation and the prescriber's order.
•	Always verify the generic name, strength, dosage form, route, directions, quantity, prescriber details and patient information before dispensing.
•	Storage instructions on the product label/manufacturer's package take priority over a generic storage recommendation.
•	An expiry date means the manufacturer has established stability only through that date under specified storage conditions. Do not use medicines after expiry.
•	Keep medicines in their original labelled containers, protected from heat, moisture and direct sunlight unless the label specifies otherwise.
2. Common Cardiovascular Drug Classes
Class	Examples	Main uses	Prescription	Key safety point
ACE inhibitors	Enalapril, Lisinopril	Hypertension; heart failure; cardiovascular/renal protection in selected patients	Prescription	Monitor BP, renal function and potassium; cough/angioedema can occur.
ARBs	Losartan, Valsartan	Hypertension; heart failure; alternative when ACE-inhibitor cough occurs	Prescription	Monitor BP, renal function and potassium.
Calcium-channel blockers	Amlodipine, Nifedipine	Hypertension; angina	Prescription	Monitor BP; ankle oedema and flushing may occur.
Thiazide/thiazide-like diuretics	Hydrochlorothiazide, Indapamide	Hypertension; fluid control in selected patients	Prescription	Monitor electrolytes, renal function and BP.
Loop diuretics	Furosemide	Oedema/heart failure; fluid overload	Prescription	Monitor fluid status, BP, electrolytes and renal function.
Beta-blockers	Bisoprolol, Metoprolol	Selected hypertension/angina/arrhythmias/heart failure	Prescription	Check pulse/BP; do not stop abruptly.
Nitrates	Glyceryl trinitrate (nitroglycerin)	Rapid relief/prevention of angina	Prescription/controlled according to product and jurisdiction	Never combine with PDE-5 inhibitors such as sildenafil because of potentially severe hypotension.
Antiplatelets	Aspirin, Clopidogrel	Prevention of arterial thrombosis in selected patients	Prescription for many indications	Bleeding risk; aspirin is not appropriate for everyone.
Anticoagulants	Warfarin, Apixaban, Rivaroxaban	Prevention/treatment of selected venous or cardioembolic thrombosis	Prescription	Bleeding risk; interactions and monitoring requirements vary.
Statins	Atorvastatin, Simvastatin	Reduction of LDL cholesterol and cardiovascular risk	Prescription	Monitor for muscle symptoms and clinically indicated laboratory tests.
 
3. Typical Adult Dose & Administration Reference
The following are common starting/maintenance examples for educational purposes. They are NOT universal prescriptions. The exact dose must be selected for the individual patient.
Medicine	Typical adult dose*	How to take	Prescription	Storage
Amlodipine	5 mg once daily; may be increased to 10 mg once daily	Swallow with water; with or without food.	Usually prescription	Room temperature; protect from excessive heat/moisture.
Enalapril	Hypertension: commonly 5 mg once daily initially; titrated by clinician	With or without food; take consistently.	Prescription	Store in original container at labelled conditions.
Losartan	Hypertension: commonly 50 mg once daily; may be adjusted	With or without food.	Prescription	Store in original container; protect from moisture.
Hydrochlorothiazide	Commonly 12.5–25 mg once daily for hypertension	Usually in the morning to reduce nocturia.	Prescription	Keep dry; follow package storage conditions.
Furosemide	Dose varies widely; often 20–40 mg initially for oedema, then individualized	Usually morning; second dose, if prescribed, earlier in day.	Prescription	Keep dry; monitor fluid/electrolytes.
Bisoprolol	Heart failure: often started very low and titrated; hypertension/angina doses commonly 5–10 mg once daily	Take at same time daily; do not stop abruptly.	Prescription	Room temperature; follow label.
Glyceryl trinitrate SL	For acute angina: commonly 0.3–0.6 mg under tongue per product instructions; repeat only as directed	Sit down; place under tongue; do not swallow. Seek urgent help if chest pain persists as instructed by emergency guidance.	Prescription/varies by product	Protect from heat/light; keep container tightly closed; follow product-specific instructions.
Aspirin (secondary prevention)	Commonly 75–100 mg once daily when specifically indicated	Take with water; food may reduce stomach irritation.	Prescription/varies by indication	Store dry; do not use after expiry.
Clopidogrel	Commonly 75 mg once daily for selected indications	With or without food; take consistently.	Prescription	Store in original pack.
Warfarin	Highly individualized; dose determined by INR and indication	Same time each day; consistent vitamin-K intake; regular INR monitoring.	Prescription	Store at room temperature in original container.
Apixaban	Commonly 5 mg twice daily for many adult indications; dose may be reduced for specific patients	With or without food; doses should be evenly spaced.	Prescription	Store at labelled room-temperature conditions.
Atorvastatin	Commonly 10–20 mg once daily initially, titrated according to risk/response	With or without food; usually once daily.	Prescription	Store in original container; protect from excessive heat/moisture.
*Dose examples are educational only and do not replace the product label, treatment guideline or patient-specific prescription.
 
4. Prescription and Dispensing Checklist
•	Confirm the patient's identity and clinical indication.
•	Check the medicine name, strength, dosage form, route, dose, frequency, duration and quantity.
•	Check allergies, pregnancy status where relevant, kidney/liver function, blood pressure/pulse and clinically important interactions.
•	Review other medicines, including OTC medicines and herbal products.
•	Explain the purpose of the medicine and exactly how and when to take it.
•	Explain common important adverse effects and which symptoms require urgent medical attention.
•	Confirm the patient understands what to do if a dose is missed; do not routinely double the next dose.
•	Document dispensing and provide appropriate follow-up/monitoring instructions.
5. Storage of Cardiovascular Medicines
Product type	Storage principle	Practical advice
Tablets/capsules	Usually at controlled room temperature as stated on label	Keep dry; avoid bathrooms, direct sunlight and excessive heat.
Nitroglycerin/GTN	Product-specific; many formulations require protection from heat/light and tightly closed packaging	Keep in original container and follow manufacturer's instructions exactly.
Liquid medicines	Product-specific	Check label after opening; some liquids have shorter in-use stability.
Refrigerated products	Only when the label specifically requires it	Do not freeze unless explicitly permitted.
All medicines	Follow labelled storage conditions	Keep out of children's reach; do not transfer to unlabelled containers.
6. Expiry Dates: What Patients and Students Must Know
•	EXP 06/2027 generally means the product should be used through the end of June 2027, unless the manufacturer states a different convention.
•	Do not use an expired medicine. Expiration dating reflects the period for which the product is known to retain required strength, quality and purity under specified storage conditions.
•	Heat, humidity, light and poor storage can reduce medicine stability even before the printed expiry date.
•	Never guess an expiry date for a loose tablet removed from its original labelled package.
•	Return expired or unwanted medicines to a pharmacy or approved medicine-disposal service where available; do not keep them for future use.
7. How Patients Should Take Cardiovascular Medicines
•	Take the medicine exactly as prescribed and at the same time each day when possible.
•	Use water unless the medicine label or pharmacist gives different instructions.
•	Do not share medicines with another person, even if they have similar symptoms.
•	Do not stop antihypertensives, beta-blockers, anticoagulants or other long-term cardiovascular medicines abruptly unless directed by a healthcare professional.
•	If a dose is missed, follow the product-specific leaflet or pharmacist's instructions; avoid automatically taking a double dose.
•	Maintain scheduled blood-pressure, pulse, kidney-function, electrolyte, lipid or INR monitoring when required.
 
8. Important Interactions & Red Flags
Combination	Potential concern	Action
ACE inhibitor/ARB + potassium-raising medicines	May increase potassium	Seek pharmacist/clinician review; monitoring may be required.
Nitrate + sildenafil/tadalafil or similar PDE-5 inhibitor	Can cause severe hypotension	Do NOT combine; seek urgent professional advice.
Anticoagulant + NSAID/other bleeding-risk medicines	May increase bleeding	Check with pharmacist/clinician before use.
Beta-blocker + other medicines lowering heart rate	May cause excessive bradycardia/hypotension	Requires professional review.
Diuretic + dehydration/other electrolyte-altering drugs	May disturb electrolytes and renal function	Maintain monitoring and follow clinician advice.
Statin + certain interacting medicines	Can increase statin toxicity/myopathy risk	Check interactions before adding medicines.
9. When to Seek Urgent Medical Care
•	Severe or new chest pain, especially with sweating, shortness of breath, nausea, fainting or pain spreading to the arm/jaw/back.
•	Sudden weakness/numbness on one side, facial drooping, difficulty speaking or sudden severe confusion—possible stroke.
•	Severe difficulty breathing, collapse or loss of consciousness.
•	Major bleeding, vomiting blood, coughing blood, black/tarry stools or severe unexplained bruising while taking antithrombotic medicines.
•	Severe swelling of the lips, tongue or throat after an ACE inhibitor or another medicine.
10. Patient Counselling Points
•	Know the generic name, strength, purpose and dosing schedule of every cardiovascular medicine you take.
•	Keep an updated medicine list and show it to every healthcare professional.
•	Check the expiry date and storage conditions before use.
•	Keep medicines in original labelled packaging where possible.
•	Lifestyle measures remain important: reduce excess salt, be physically active as medically appropriate, avoid tobacco, maintain a healthy weight and follow a heart-healthy diet.
•	Never use someone else's blood-pressure, heart or blood-thinning medicine.
11. Selected References
•	World Health Organization. Guideline for the pharmacological treatment of hypertension in adults (2021/2022).
•	World Health Organization. Cardiovascular diseases (CVDs) fact sheet.
•	U.S. Food and Drug Administration. Expiration Dates – Questions and Answers.
•	U.S. Food and Drug Administration. Think It Through: Managing the Benefits and Risks of Medicines.
•	Always consult the current product Summary of Product Characteristics/package insert and applicable Ugandan/national treatment guidelines before prescribing or dispensing.
EDUCATIONAL USE ONLY • NOT A SUBSTITUTE FOR A PATIENT-SPECIFIC PRESCRIPTION

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
