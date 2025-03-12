---
title: "Mediadesign"
date: "2025-03-12T00:00:00Z"
draft: false
description: "Mediadesign"
isStarred: false
layout: "monthly_overview"
---

# Modernes Webdesign
## Einleitung
In diesem Beitrag möchte ich euch einen detaillierten Einblick in die Entwicklung meiner Portfolio-Webseite geben, die ich mit einem modernen Design, Smooth Scrolling und nahtlosen Seitentransitionen ausgestattet habe. Die Herausforderung bestand darin, eine übersichtliche und schnelle Navigation zu schaffen, die nicht nur auf Desktop-Geräten, sondern auch auf mobilen Geräten optimal funktioniert.
Dabei habe ich verschiedene moderne Web-Technologien eingesetzt, um ein ansprechendes Nutzererlebnis zu schaffen. Hier erkläre ich euch Schritt für Schritt, wie ich die wichtigsten Funktionen umgesetzt habe:

- Dynamisches Laden von Navigation und Footer
- Responsives Burgermenü

## Projektstruktur
Die Projektstruktur ist relativ einfach aufgebaut. Es gibt eine Hauptseite (index.html) und eine Unterseite für Projekte (projekte.html). Navigation und Footer werden dynamisch über JavaScript geladen, um Redundanzen zu vermeiden und eine zentrale Verwaltung zu ermöglichen.

📂 **Projektstruktur**
```
📁 project-root
├── 📁 assets
│   ├── 📁 img
│   ├── 📁 projekte
├── 📁 styles
├── index.html
├── projekte.html
├── navigation.html
├── footer.html
├── styles.css
├── projekte.css
└── scripts.js
```

## Dynamisches Laden von Navigation und Footer
Anstatt die Navigation und den Footer auf jeder Seite zu duplizieren, habe ich sie in separate HTML-Dateien ausgelagert (navigation.html und footer.html) und dynamisch mit fetch() geladen. Dies vereinfacht die Pflege und Aktualisierung erheblich.

**navigation.html**
```html
<nav>
  <div class="nav-container">
    <a href="index.html" class="transition">
      <img src="assets/img/logo.png" alt="Kay Beckmann Webdesign Logo" class="logo">
    </a>
    <button id="burger-button" class="burger-button" aria-label="Menü öffnen">&#9776;</button>
    <ul class="nav-links">
      <li><a href="index.html#landing" class="transition">Home</a></li>
      <li><a href="index.html#services" class="transition">Dienstleistungen</a></li>
      <li><a href="index.html#contact" class="transition">Kontakt</a></li>
      <li><a href="projekte.html" class="transition">Projekte</a></li>
    </ul>
  </div>
</nav>
```

**scripts.js**

Im JavaScript wird der fetch()-Befehl verwendet, um die HTML-Dateien einzubinden:

```javascript
document.addEventListener('DOMContentLoaded', () => {
  // Navigation dynamisch laden
  fetch('navigation.html')
    .then(response => response.text())
    .then(data => {
      document.getElementById('nav-placeholder').innerHTML = data;
      initBurgerMenu();
      initSmoothScrolling();
    });

  // Footer dynamisch laden
  fetch('footer.html')
    .then(response => response.text())
    .then(data => {
      document.getElementById('footer-placeholder').innerHTML = data;
    });
});
```

## Smooth Scrolling für interne Links
Smooth Scrolling sorgt dafür, dass der Übergang zu internen Ankern (z. B. #services) nicht abrupt, sondern mit einer sanften Animation erfolgt.

### JavaScript für Smooth Scrolling:
Hier wird geprüft, ob ein interner Link vorhanden ist. Falls ja, wird der Browser zum Ziel-Element gescrollt:

```javascript
function initSmoothScrolling() {
  const navLinks = document.querySelectorAll('nav a');
  const burgerButton = document.getElementById("burger-button");

  navLinks.forEach(link => {
    link.addEventListener('click', function(e) {
      const href = link.getAttribute('href');

      if (href.includes('#')) {
        const [base, targetId] = href.split('#');
        const currentPage = window.location.pathname.replace(/^\//, '');

        if (base === "" || base === currentPage || (currentPage === "" && base === "index.html")) {
          e.preventDefault();
          const targetElement = document.getElementById(targetId);
          if (targetElement) {
            window.scrollTo({
              top: targetElement.offsetTop - document.querySelector('header').offsetHeight,
              behavior: 'smooth'
            });
          }
          // Menü in mobiler Ansicht schließen:
          const mobileNav = document.querySelector('.nav-links.show');
          if (mobileNav && burgerButton) {
            mobileNav.classList.remove('show');
            burgerButton.setAttribute("aria-label", "Menü öffnen");
          }
        }
      }
    });
  });
}
```

## Nahtlose Seitentransitionen (Fade-In und Fade-Out)
Um die Übergänge zwischen Seiten nahtlos zu gestalten, habe ich eine einfache CSS-Transition mit JavaScript kombiniert.

### CSS für Transition:
```css
body {
  opacity: 1;
  transition: opacity 0.5s ease-in-out;
}

body.fade-out {
  opacity: 0;
}
```

### JavaScript für die Transition:
Das JavaScript fügt die Klasse fade-out hinzu und wechselt nach Ablauf der Transition zur Zielseite:

```javascript
document.addEventListener('DOMContentLoaded', () => {
  const transitionLinks = document.querySelectorAll('a.transition');

  transitionLinks.forEach(link => {
    link.addEventListener('click', (e) => {
      e.preventDefault();
      const target = link.getAttribute('href');
      document.body.classList.add('fade-out');
      setTimeout(() => {
        window.location.href = target;
      }, 500);
    });
  });
});
```

Im HTML müssen die Links, die mit einer Transition versehen werden sollen, die Klasse *transition* erhalten:

```html
<a href="projekte.html" class="transition">Projekte</a>
```

## Responsives Burgermenü
Für die mobile Ansicht habe ich ein Burgermenü implementiert, das sich ein- und ausklappen lässt:

### CSS für das Menü:
```css
.burger-button {
  display: none;
}

@media (max-width: 768px) {
  .burger-button {
    display: block;
  }
  .nav-links {
    display: none;
    flex-direction: column;
  }
  .nav-links.show {
    display: flex;
  }
}
```

### JavaScript für das Menü:
```javascript
function initBurgerMenu() {
  const burgerButton = document.getElementById("burger-button");
  const navLinks = document.querySelector(".nav-links");

  if (burgerButton && navLinks) {
    burgerButton.addEventListener("click", () => {
      navLinks.classList.toggle("show");
    });
  }
}
```

## Ergebnis
✅ Nahtlose Navigation zwischen Seiten
✅ Elegantes Smooth Scrolling
✅ Zentrales Management der Navigation und des Footers
✅ Responsives Design und Burgermenü für mobile Endgeräte

# Fazit
Die Kombination aus dynamischem Content-Loading, Smooth Scrolling und nahtlosen Transitionen sorgt für ein modernes und ansprechendes Nutzererlebnis. Der Code ist modular aufgebaut, leicht erweiterbar und kann problemlos für weitere Projekte angepasst werden.
Falls ihr ähnliche Funktionen in euren Projekten umsetzen möchtet, könnt ihr den Code gerne als Grundlage verwenden. Viel Spaß beim Coden! 😎
