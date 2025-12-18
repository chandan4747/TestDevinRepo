# Creative Designs by Kushi Enterprises

A beautiful, responsive landing page for an embroidery business built with Python Flask. This website showcases embroidery services, portfolio gallery, company information, and contact details with an elegant design featuring warm brown and cream tones that complement the embroidery theme.

## Features

- **Responsive Design**: Fully responsive layout that works seamlessly on desktop, tablet, and mobile devices
- **Modern UI**: Clean, professional design with smooth animations and hover effects
- **Hero Section**: Eye-catching landing area with business tagline and call-to-action button
- **Services Section**: Showcases four key service offerings with attractive icons
- **Gallery Section**: Portfolio display with image hover effects
- **About Section**: Company story with statistics (clients served, projects completed, years of experience)
- **Contact Section**: Contact information and inquiry form
- **Fixed Navigation**: Sticky navigation bar with smooth scroll to sections

## Tech Stack

- **Backend**: Python 3, Flask
- **Frontend**: HTML5, CSS3
- **Fonts**: Google Fonts (Playfair Display, Poppins)
- **Styling**: Custom CSS with CSS Grid and Flexbox

## Project Structure

```
kushi-embroidery-python/
├── app.py                 # Flask application entry point
├── requirements.txt       # Python dependencies
├── README.md             # Project documentation
├── templates/
│   └── index.html        # Main landing page template
└── static/
    ├── style.css         # CSS styles
    └── images/           # Image assets
        ├── embroidery1.jpg
        └── about.jpg
```

## Prerequisites

Before you begin, ensure you have the following installed:

- Python 3.8 or higher
- pip (Python package manager)

## Installation & Setup

### Step 1: Clone the Repository

```bash
git clone https://github.com/chandan4747/TestDevinRepo.git
cd TestDevinRepo
```

### Step 2: Create a Virtual Environment (Recommended)

**On macOS/Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

**On Windows:**
```bash
python -m venv venv
venv\Scripts\activate
```

### Step 3: Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 4: Run the Application

```bash
python app.py
```

The application will start and be available at `http://localhost:5000`

## Running in Production

For production deployment, use Gunicorn (included in requirements.txt):

```bash
gunicorn -w 4 -b 0.0.0.0:5000 app:app
```

## Customization

### Updating Business Information

Edit `templates/index.html` to update:
- Business name and tagline
- Service descriptions
- Contact information (address, phone, email)
- Working hours
- Statistics (clients, projects, years)

### Changing Colors

The color scheme can be modified in `static/style.css`. Key color variables used:
- Primary brown: `#8b4513`
- Secondary brown: `#a0522d`
- Dark brown: `#2c1810`
- Cream/beige backgrounds: `#faf8f5`, `#f5e6d3`, `#e8d5c4`

### Adding Gallery Images

1. Add your images to `static/images/`
2. Update the image paths in `templates/index.html` in the gallery section
3. Images should ideally be square (1:1 aspect ratio) for best display

## Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)

## License

This project is open source and available for personal and commercial use.

## Contact

For questions or support regarding this project, please open an issue on GitHub.

---

Built with care for **Kushi Enterprises** - Transforming fabric into art with exquisite embroidery craftsmanship.
