from flask import Flask, render_template_string, request

app = Flask(__name__)

# Listing data
LISTINGS = [
    {
        "id": 1,
        "title": "Oceanview Villa, Goa",
        "price": "₹2.9 Cr",
        "lat": 15.4909,
        "lng": 73.8278,
        "img": "https://images.unsplash.com/photo-1568605114967-8130f3a36994?auto=format&fit=crop&w=1400&q=80",
        "desc": "5 BHK · Infinity Pool · Private Beach Access"
    },
    {
        "id": 2,
        "title": "Skyline Penthouse, Mumbai",
        "price": "₹1.1 Cr",
        "lat": 19.0760,
        "lng": 72.8777,
        "img": "https://images.unsplash.com/photo-1542314831-068cd1dbfeeb?auto=format&fit=crop&w=1400&q=80",
        "desc": "3 BHK · Panoramic City View · Concierge"
    },
    {
        "id": 3,
        "title": "Eco Smart Home, Bangalore",
        "price": "₹88 Lakh",
        "lat": 12.9716,
        "lng": 77.5946,
        "img": "https://images.unsplash.com/photo-1572120360610-d971b9b78825?auto=format&fit=crop&w=1400&q=80",
        "desc": "3 BHK · Solar · Smart Lighting"
    },
    {
        "id": 4,
        "title": "Garden Bungalow, Kolkata",
        "price": "₹1.30 Cr",
        "lat": 22.5726,
        "lng": 88.3639,
        "img": "https://images.unsplash.com/photo-1600585154356-596af9009e3e?auto=format&fit=crop&w=1400&q=80",
        "desc": "4 BHK · Private Garden · Gated Community"
    }
]

BASE = """
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>{{ title }} — Realty Demo</title>
  <link
    rel="stylesheet"
    href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
    integrity="sha256-Cu6LS-…"
    crossorigin=""
  />
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <style>
    body { margin:0; font-family:Arial,sans-serif; }
    header { background:#004aad; color:white; padding:20px; text-align:center; }
    nav a { margin: 0 10px; color:white; text-decoration:none; }
    .map { height: 300px; }
    .listing { margin:20px; }
    .card { border:1px solid #ddd; padding:10px; margin-bottom:10px; }
    .card img { max-width:100%; height:auto; }
    footer { text-align:center; padding:20px; background:#004aad; color:white; }
  </style>
</head>
<body>
  <header>
    <h1>Realty Demo</h1>
    <nav>
      <a href="/">Home</a>
      <a href="/listings">Listings</a>
      <a href="/contact">Contact</a>
    </nav>
  </header>
  <main>
    {{ content|safe }}
  </main>
  <footer>
    © 2025 Realty Demo
  </footer>
  <script>
    // Leaflet map setup for page where id="map" is present
    if (document.getElementById('map')) {
      var map = L.map('map').setView([20,77],5);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        maxZoom:19,
      }).addTo(map);
      var listings = {{ listings|tojson }};
      listings.forEach(function(it){
        L.marker([it.lat, it.lng]).addTo(map)
          .bindPopup(`<b>${it.title}</b><br>${it.price}`);
      });
    }
  </script>
</body>
</html>
"""

HOME = """
<section style="padding:20px;">
  <h2>Welcome to Realty Demo</h2>
  <p>Browse featured properties.</p>
</section>
"""

LISTINGS_PAGE = """
<section style="padding:20px;">
  <div id="map" class="map"></div>
  <div class="listing">
    {% for it in listings %}
      <div class="card">
        <h3>{{ it.title }}</h3>
        <img src="{{ it.img }}" alt="">
        <p>{{ it.desc }}</p>
        <p><b>{{ it.price }}</b></p>
      </div>
    {% endfor %}
  </div>
</section>
"""

CONTACT_PAGE = """
<section style="padding:20px;">
  <h2>Contact Us</h2>
  {% if sent %}
    <p>Thanks {{ name }}! We got your message (demo).</p>
  {% endif %}
  <form method="post" action="/contact">
    <input type="text" name="name" placeholder="Your name" required><br>
    <input type="email" name="email" placeholder="Your email" required><br>
    <textarea name="message" placeholder="Your message"></textarea><br>
    <button type="submit">Send</button>
  </form>
</section>
"""

from flask import json

@app.route("/")
def home():
    return render_template_string(BASE, title="Home", content=HOME, listings=LISTINGS)

@app.route("/listings")
def listings_page():
    return render_template_string(BASE, title="Listings", content=LISTINGS_PAGE, listings=LISTINGS)

@app.route("/contact", methods=["GET","POST"])
def contact():
    name = ""
    sent = False
    if request.method == "POST":
        name = request.form.get("name","")
        sent = True
    return render_template_string(BASE, title="Contact", content=CONTACT_PAGE, sent=sent, name=name, listings=LISTINGS)

if __name__ == "__main__":
    app.run(debug=True)
