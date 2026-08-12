import React, { useState } from "react";

/* =============================================================================
 * PROGRÈS SERVICE — site vitrine (centre de formation)
 * =============================================================================
 * Site statique, une seule page, sans base de données : c'est un site
 * "vitrine" destiné à informer et convertir vers WhatsApp, pas une
 * application avec compte/connexion. Toutes les informations viennent du
 * brief fourni par le client (Progrès Service) — rien n'est inventé, y
 * compris le numéro de contact.
 * ============================================================================= */

const WHATSAPP_NUMBER = "22677356749"; // +226 77 35 67 49
const PHONE_DISPLAY = "77 35 67 49";
const ADDRESS = "Ouagadougou, RN4 / Gampela — à l'Est de la Porte de l'Université UPG";

const WHATSAPP_MESSAGE = "Bonjour Progrès Service, je souhaite avoir plus d'informations concernant vos formations et m'inscrire.";
const waLink = () => `https://wa.me/${WHATSAPP_NUMBER}?text=${encodeURIComponent(WHATSAPP_MESSAGE)}`;

const MODULES = [
  {
    code: "M1",
    title: "Informatique",
    price: 10000,
    items: ["Windows", "MS Word", "MS Excel", "MS PowerPoint", "Internet & Publisher", "Technique de rédaction (CV, lettre de motivation)", "Canva"],
  },
  {
    code: "M2",
    title: "Secrétariat / Administration",
    price: 20000,
    items: ["Gestion de l'accueil", "Photocopie, reliure, scannage, impression", "Classement et archivage des documents", "Gestion de standard / rendez-vous", "Production de documents administratifs", "Entretien d'embauche", "Gestion des tâches RH"],
  },
  {
    code: "M3",
    title: "Infographie",
    price: 20000,
    items: ["Maîtrise de l'interface du logiciel", "Maîtrise des calques", "Couleurs, formes et textes", "Conception d'affiches", "Conception d'étiquettes", "Conception de cartes diverses", "Conception de flyers", "Conception de dépliants"],
  },
  {
    code: "M4",
    title: "Gestion de caisse",
    price: 25000,
    items: ["Encaissement", "Décaissement", "Gestion de la caisse sur Excel", "Contrôle de la caisse"],
  },
  {
    code: "M5",
    title: "Informatique avancée",
    price: 20000,
    items: ["Tableaux / graphique dynamique croisé", "Utilisation Monnaie (F, $, dollars)", "Classeur automatique avec Excel", "Calendriers & cartes Pro", "Mise en forme avec MS Excel", "Calcul des dates, pourcentages & ALEA", "Fonctions SI, V, H", "Calcul de rang, mention, décision, Max & Min", "Réalisation de graphiques, courbes"],
  },
  {
    code: "M6",
    title: "Gestion des réseaux sociaux",
    price: 10000,
    items: ["Création de comptes Facebook, TikTok, Instagram, YouTube", "Monétisation / vente de comptes", "Gestion d'un ancien compte", "Publicité sur Meta Business", "Création de la bio", "Postages / campagnes publicitaires"],
  },
];

const TOTAL_PRICE = 60000;
const REGISTRATION_FEE = 1000;

const fmt = (n) => new Intl.NumberFormat("fr-FR").format(n) + " FCFA";

/* ---------- ICÔNES ---------- */
const Icon = {
  chat: (p) => (
    <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" strokeWidth="1.8" {...p}>
      <path d="M4 12a8 8 0 1 1 3.2 6.4L4 20l1.3-3.6A7.96 7.96 0 0 1 4 12Z" strokeLinecap="round" strokeLinejoin="round" />
    </svg>
  ),
  clock: (p) => (
    <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" strokeWidth="1.7" {...p}>
      <circle cx="12" cy="12" r="9" />
      <path d="M12 7v5l3.5 2" strokeLinecap="round" strokeLinejoin="round" />
    </svg>
  ),
  calendar: (p) => (
    <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" strokeWidth="1.7" {...p}>
      <rect x="3.5" y="5" width="17" height="16" rx="2" />
      <path d="M3.5 9.5h17M8 3v4M16 3v4" strokeLinecap="round" />
    </svg>
  ),
  hourglass: (p) => (
    <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" strokeWidth="1.7" {...p}>
      <path d="M6 3h12M6 21h12M7 3c0 5 5 6 5 9s-5 4-5 9M17 3c0 5-5 6-5 9s5 4 5 9" strokeLinecap="round" strokeLinejoin="round" />
    </svg>
  ),
  location: (p) => (
    <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" strokeWidth="1.7" {...p}>
      <path d="M12 21s7-6.5 7-11.5a7 7 0 1 0-14 0C5 14.5 12 21 12 21Z" strokeLinejoin="round" />
      <circle cx="12" cy="9.5" r="2.4" />
    </svg>
  ),
  monitor: (p) => (
    <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" strokeWidth="1.7" {...p}>
      <rect x="3" y="4.5" width="18" height="12" rx="1.5" />
      <path d="M8 20h8M12 16.5V20" strokeLinecap="round" />
    </svg>
  ),
  check: (p) => (
    <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" strokeWidth="2.2" {...p}>
      <path d="m5 13 4.5 4.5L19 7" strokeLinecap="round" strokeLinejoin="round" />
    </svg>
  ),
  phone: (p) => (
    <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" strokeWidth="1.8" {...p}>
      <path d="M6.5 3.5h3l1.5 4.5-2.2 1.8a13.5 13.5 0 0 0 6.4 6.4l1.8-2.2 4.5 1.5v3a2 2 0 0 1-2.1 2C11.6 20 4 12.4 4.5 5.6a2 2 0 0 1 2-2.1Z" strokeLinejoin="round" />
    </svg>
  ),
  menu: (p) => (
    <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" strokeWidth="1.8" {...p}>
      <path d="M4 7h16M4 12h16M4 17h16" strokeLinecap="round" />
    </svg>
  ),
  close: (p) => (
    <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" strokeWidth="2" {...p}>
      <path d="M6 6l12 12M18 6 6 18" strokeLinecap="round" />
    </svg>
  ),
};

/* ---------- Illustration hero (géométrique, pas de photo empruntée) ---------- */
function HeroIllustration() {
  return (
    <svg viewBox="0 0 360 320" style={{ width: "100%", maxWidth: 380, height: "auto" }}>
      <rect x="40" y="30" width="280" height="180" rx="14" fill="#1A1A1A" />
      <rect x="58" y="48" width="244" height="144" rx="6" fill="#FFFFFF" />
      <rect x="76" y="66" width="120" height="10" rx="5" fill="#C81E2C" />
      <rect x="76" y="86" width="180" height="8" rx="4" fill="#E5E5E5" />
      <rect x="76" y="102" width="150" height="8" rx="4" fill="#E5E5E5" />
      <rect x="76" y="118" width="190" height="8" rx="4" fill="#E5E5E5" />
      <circle cx="230" cy="150" r="26" fill="#C81E2C" opacity="0.12" />
      <path d="m218 150 8 8 16-16" stroke="#C81E2C" strokeWidth="4" strokeLinecap="round" strokeLinejoin="round" fill="none" />
      <rect x="150" y="222" width="60" height="14" rx="7" fill="#1A1A1A" />
      <rect x="120" y="210" width="120" height="10" rx="5" fill="#F4F4F4" />
      <circle cx="322" cy="60" r="18" fill="#C81E2C" />
      <circle cx="40" cy="230" r="12" fill="#C81E2C" opacity="0.25" />
      <circle cx="330" cy="250" r="8" fill="#1A1A1A" opacity="0.15" />
    </svg>
  );
}

/* ---------- Badge "sceau" pour les modules et certificats ---------- */
function Seal({ children, size = 54, filled = true }) {
  return (
    <div
      style={{
        width: size,
        height: size,
        borderRadius: "50%",
        background: filled ? "#C81E2C" : "#fff",
        color: filled ? "#fff" : "#C81E2C",
        border: filled ? "none" : "2px solid #C81E2C",
        display: "flex",
        alignItems: "center",
        justifyContent: "center",
        fontFamily: "'Sora', sans-serif",
        fontWeight: 800,
        fontSize: size * 0.32,
        flex: "0 0 auto",
      }}
    >
      {children}
    </div>
  );
}

function WhatsAppButton({ children, style, big }) {
  return (
    <a
      href={waLink()}
      target="_blank"
      rel="noreferrer"
      className="btn btn-primary"
      style={{ fontSize: big ? 15 : 13.5, padding: big ? "16px 30px" : "12px 22px", ...style }}
    >
      <Icon.chat style={{ width: big ? 20 : 16, height: big ? 20 : 16 }} /> {children}
    </a>
  );
}

export default function App() {
  const [menuOpen, setMenuOpen] = useState(false);

  const scrollTo = (id) => {
    setMenuOpen(false);
    document.getElementById(id)?.scrollIntoView({ behavior: "smooth", block: "start" });
  };

  return (
    <div className="page">
      <GlobalStyle />

      {/* ---------- HEADER ---------- */}
      <header className="header">
        <div className="container header-inner">
          <div className="logo" onClick={() => scrollTo("accueil")}>
            PROGRÈS<span>SERVICE</span>
          </div>
          <nav className="nav-desktop">
            <button onClick={() => scrollTo("formations")}>Formations</button>
            <button onClick={() => scrollTo("apropos")}>À propos</button>
            <button onClick={() => scrollTo("tarifs")}>Tarifs</button>
            <button onClick={() => scrollTo("contact")}>Contact</button>
          </nav>
          <div className="header-actions">
            <WhatsAppButton>S'inscrire</WhatsAppButton>
            <button className="menu-btn" onClick={() => setMenuOpen(true)} aria-label="Menu"><Icon.menu /></button>
          </div>
        </div>
      </header>

      {menuOpen && (
        <div className="mobile-menu">
          <button className="close-btn" onClick={() => setMenuOpen(false)}><Icon.close /></button>
          <button onClick={() => scrollTo("accueil")}>Accueil</button>
          <button onClick={() => scrollTo("formations")}>Formations</button>
          <button onClick={() => scrollTo("apropos")}>À propos</button>
          <button onClick={() => scrollTo("tarifs")}>Tarifs</button>
          <button onClick={() => scrollTo("inscription")}>Inscription</button>
          <button onClick={() => scrollTo("contact")}>Contact</button>
        </div>
      )}

      {/* ---------- HERO ---------- */}
      <section id="accueil" className="hero container">
        <div className="hero-text">
          <div className="eyebrow">Centre de formation professionnelle</div>
          <h1>
            PROGRÈS <span className="ink">SERVICE</span>
          </h1>
          <div className="slogan">Apprendre — Évoluer — Réussir</div>
          <p className="hero-desc">
            Développez vos compétences grâce à des formations pratiques et accessibles,
            en présentiel, en ligne ou à domicile.
          </p>
          <div className="hero-actions">
            <button className="btn btn-outline" onClick={() => scrollTo("formations")}>Voir nos formations</button>
            <WhatsAppButton big>S'inscrire maintenant</WhatsAppButton>
          </div>
        </div>
        <div className="hero-visual"><HeroIllustration /></div>
      </section>

      {/* ---------- FORMATIONS ---------- */}
      <section id="formations" className="section">
        <div className="container">
          <div className="eyebrow center">Nos formations</div>
          <h2 className="section-title center">6 modules, un seul objectif : votre réussite</h2>
          <div className="grid-modules">
            {MODULES.map((m) => (
              <div key={m.code} className="card module-card">
                <div className="module-head">
                  <Seal>{m.code}</Seal>
                  <div className="module-price">{fmt(m.price)}</div>
                </div>
                <h3>{m.title}</h3>
                <ul>
                  {m.items.map((it) => (
                    <li key={it}><Icon.check className="check-ic" />{it}</li>
                  ))}
                </ul>
              </div>
            ))}
          </div>
        </div>
      </section>

      {/* ---------- INFOS PRATIQUES ---------- */}
      <section id="apropos" className="section alt">
        <div className="container">
          <div className="eyebrow center">Informations pratiques</div>
          <h2 className="section-title center">Une formation pensée pour votre emploi du temps</h2>
          <div className="grid-info">
            <div className="info-card">
              <Icon.calendar className="info-ic" />
              <div className="info-label">Formation</div>
              <div className="info-value">Tous les jours — 6 jours / 7</div>
            </div>
            <div className="info-card">
              <Icon.clock className="info-ic" />
              <div className="info-label">Horaires</div>
              <div className="info-value">09h–11h · 12h–14h · 19h–21h</div>
            </div>
            <div className="info-card">
              <Icon.hourglass className="info-ic" />
              <div className="info-label">Durée</div>
              <div className="info-value">01 mois et demi</div>
            </div>
            <div className="info-card">
              <Icon.location className="info-ic" />
              <div className="info-label">Modalités</div>
              <div className="info-value">Présentiel · En ligne · À domicile</div>
            </div>
            <div className="info-card">
              <Icon.monitor className="info-ic" />
              <div className="info-label">Équipement</div>
              <div className="info-value">Ordinateurs et Wi-Fi disponibles</div>
            </div>
          </div>
        </div>
      </section>

      {/* ---------- AVANTAGES ---------- */}
      <section className="section">
        <div className="container">
          <div className="eyebrow center">Vos avantages</div>
          <h2 className="section-title center">Pourquoi choisir Progrès Service ?</h2>
          <div className="grid-avantages">
            {[
              "100 % pratique",
              "Accompagnement vers l'emploi",
              "Clé de connexion offerte",
              "Support de cours + T-shirt offert",
              "Bonus : adhésion au groupe des grands entrepreneurs et des affaires",
            ].map((a) => (
              <div key={a} className="avantage-item">
                <Seal size={38}><Icon.check style={{ width: 18, height: 18 }} /></Seal>
                <span>{a}</span>
              </div>
            ))}
          </div>
        </div>
      </section>

      {/* ---------- PROMOTION / CERTIFICATION ---------- */}
      <section className="section promo">
        <div className="container promo-inner">
          <Seal size={70} filled={false}>22</Seal>
          <div className="eyebrow" style={{ color: "#fff" }}>22ème édition</div>
          <h2 className="promo-title">06 certificats reconnus à la clé</h2>
          <div className="promo-tags">
            {["Informatique I & II", "Infographie", "Secrétaire administrative", "Secrétaire caissière", "Marketing digital"].map((c) => (
              <span key={c} className="promo-tag">{c}</span>
            ))}
          </div>
        </div>
      </section>

      {/* ---------- TARIFS ---------- */}
      <section id="tarifs" className="section alt">
        <div className="container">
          <div className="eyebrow center">Nos tarifs</div>
          <h2 className="section-title center">Un prix clair pour chaque module</h2>
          <div className="grid-tarifs">
            {MODULES.map((m) => (
              <div key={m.code} className="tarif-row">
                <div className="tarif-left">
                  <Seal size={40}>{m.code}</Seal>
                  <span>{m.title}</span>
                </div>
                <div className="tarif-price">{fmt(m.price)}</div>
              </div>
            ))}
          </div>
          <div className="total-box">
            <div>
              <div className="total-label">Coût total (6 modules)</div>
              <div className="total-value">{fmt(TOTAL_PRICE)}</div>
            </div>
            <div className="total-divider" />
            <div>
              <div className="total-label">Frais d'inscription</div>
              <div className="total-value">{fmt(REGISTRATION_FEE)}</div>
            </div>
          </div>
        </div>
      </section>

      {/* ---------- INSCRIPTION ---------- */}
      <section id="inscription" className="section cta-section">
        <div className="container cta-inner">
          <h2 className="cta-title">Prêt à vous former ?</h2>
          <p className="cta-desc">
            Inscrivez-vous dès maintenant et développez les compétences nécessaires
            pour votre avenir professionnel.
          </p>
          <WhatsAppButton big style={{ background: "#fff", color: "#C81E2C" }}>
            S'inscrire sur WhatsApp
          </WhatsAppButton>
        </div>
      </section>

      {/* ---------- CONTACT ---------- */}
      <section id="contact" className="section">
        <div className="container">
          <div className="eyebrow center">Contact</div>
          <h2 className="section-title center">Où et comment nous joindre</h2>
          <div className="contact-grid">
            <div className="contact-info">
              <div className="contact-item">
                <Icon.location className="info-ic" />
                <div>
                  <div className="info-label">Adresse</div>
                  <div className="info-value">{ADDRESS}</div>
                </div>
              </div>
              <div className="contact-item">
                <Icon.phone className="info-ic" />
                <div>
                  <div className="info-label">Téléphone</div>
                  <div className="info-value">{PHONE_DISPLAY}</div>
                </div>
              </div>
              <div className="contact-item">
                <Icon.chat className="info-ic" />
                <div>
                  <div className="info-label">WhatsApp</div>
                  <div className="info-value">{PHONE_DISPLAY}</div>
                </div>
              </div>
              <WhatsAppButton style={{ marginTop: 8, alignSelf: "flex-start" }}>Écrire sur WhatsApp</WhatsAppButton>
            </div>
            <div className="map-wrap">
              <iframe
                title="Localisation Progrès Service"
                src="https://maps.google.com/maps?q=Universit%C3%A9%20Priv%C3%A9e%20de%20Gampela%20UPG%2C%20Ouagadougou&output=embed"
                style={{ border: 0, width: "100%", height: "100%" }}
                loading="lazy"
              />
            </div>
          </div>
        </div>
      </section>

      {/* ---------- FOOTER ---------- */}
      <footer className="footer">
        <div className="container footer-inner">
          <div>
            <div className="logo footer-logo">PROGRÈS<span>SERVICE</span></div>
            <div className="footer-slogan">Apprendre – Évoluer – Réussir</div>
          </div>
          <div className="footer-links">
            <button onClick={() => scrollTo("accueil")}>Accueil</button>
            <button onClick={() => scrollTo("formations")}>Formations</button>
            <button onClick={() => scrollTo("apropos")}>À propos</button>
            <button onClick={() => scrollTo("inscription")}>Inscription</button>
            <button onClick={() => scrollTo("contact")}>Contact</button>
          </div>
        </div>
        <div className="footer-bottom">© {new Date().getFullYear()} Progrès Service — Tous droits réservés</div>
      </footer>

      {/* ---------- WHATSAPP FLOTTANT ---------- */}
      <a href={waLink()} target="_blank" rel="noreferrer" className="floating-wa" aria-label="Écrire sur WhatsApp">
        <Icon.chat style={{ width: 26, height: 26 }} />
      </a>
    </div>
  );
}

function GlobalStyle() {
  return (
    <style>{`
      @import url('https://fonts.googleapis.com/css2?family=Sora:wght@600;700;800&family=Inter:wght@400;500;600;700&display=swap');

      * { box-sizing: border-box; margin: 0; padding: 0; }
      html { scroll-behavior: smooth; }
      body { -webkit-font-smoothing: antialiased; }
      button { font-family: inherit; cursor: pointer; border: none; background: none; }
      a { text-decoration: none; color: inherit; }
      ul { list-style: none; }

      .page { font-family: 'Inter', -apple-system, sans-serif; color: #1A1A1A; background: #fff; overflow-x: hidden; }
      .container { max-width: 1080px; margin: 0 auto; padding: 0 20px; }

      .eyebrow { font-family: 'Sora', sans-serif; font-size: 12px; font-weight: 700; letter-spacing: 2px; color: #C81E2C; text-transform: uppercase; margin-bottom: 10px; }
      .eyebrow.center { text-align: center; }
      .section-title { font-family: 'Sora', sans-serif; font-size: 26px; font-weight: 800; line-height: 1.25; margin-bottom: 36px; }
      .section-title.center { text-align: center; max-width: 560px; margin-left: auto; margin-right: auto; }
      .section { padding: 64px 0; }
      .section.alt { background: #F9F7F6; }

      .btn { display: inline-flex; align-items: center; justify-content: center; gap: 8px; padding: 12px 22px; border-radius: 10px; font-family: 'Sora', sans-serif; font-weight: 700; font-size: 13.5px; white-space: nowrap; }
      .btn-primary { background: #C81E2C; color: #fff; }
      .btn-outline { background: #fff; color: #1A1A1A; border: 1.5px solid #1A1A1A; }

      /* Header */
      .header { position: sticky; top: 0; background: rgba(255,255,255,0.95); backdrop-filter: blur(6px); z-index: 40; border-bottom: 1px solid #eee; }
      .header-inner { display: flex; align-items: center; justify-content: space-between; padding-top: 14px; padding-bottom: 14px; }
      .logo { font-family: 'Sora', sans-serif; font-weight: 800; font-size: 16px; letter-spacing: -0.3px; cursor: pointer; }
      .logo span { color: #C81E2C; }
      .nav-desktop { display: none; gap: 26px; }
      .nav-desktop button { font-size: 13.5px; font-weight: 600; color: #1A1A1A; }
      .header-actions { display: flex; align-items: center; gap: 12px; }
      .menu-btn { display: flex; }

      .mobile-menu { position: fixed; inset: 0; background: #fff; z-index: 60; display: flex; flex-direction: column; padding: 20px; gap: 4px; }
      .mobile-menu .close-btn { align-self: flex-end; margin-bottom: 20px; }
      .mobile-menu button { text-align: left; font-family: 'Sora', sans-serif; font-size: 20px; font-weight: 700; padding: 14px 4px; border-bottom: 1px solid #f0f0f0; }

      /* Hero */
      .hero { display: flex; flex-direction: column; align-items: center; text-align: center; gap: 30px; padding: 48px 20px 40px; }
      .hero h1 { font-family: 'Sora', sans-serif; font-weight: 800; font-size: 34px; letter-spacing: -0.5px; line-height: 1.1; color: #C81E2C; }
      .hero h1 .ink { color: #1A1A1A; }
      .slogan { font-family: 'Sora', sans-serif; font-weight: 700; font-size: 15px; letter-spacing: 1px; color: #6B6B6B; margin: 10px 0 14px; text-transform: uppercase; }
      .hero-desc { font-size: 15px; line-height: 1.6; color: #4a4a4a; max-width: 460px; margin: 0 auto 22px; }
      .hero-actions { display: flex; flex-wrap: wrap; gap: 12px; justify-content: center; }
      .hero-visual { max-width: 320px; }

      /* Modules */
      .grid-modules { display: grid; grid-template-columns: 1fr; gap: 18px; }
      .module-card { background: #fff; border: 1px solid #eee; border-radius: 16px; padding: 22px; box-shadow: 0 1px 3px rgba(0,0,0,0.03); }
      .module-head { display: flex; align-items: center; justify-content: space-between; margin-bottom: 14px; }
      .module-price { font-family: 'Sora', sans-serif; font-weight: 800; font-size: 16px; color: #C81E2C; }
      .module-card h3 { font-family: 'Sora', sans-serif; font-size: 17px; font-weight: 700; margin-bottom: 12px; }
      .module-card ul { display: flex; flex-direction: column; gap: 8px; }
      .module-card li { display: flex; align-items: flex-start; gap: 8px; font-size: 13px; color: #4a4a4a; line-height: 1.4; }
      .check-ic { color: #C81E2C; flex: 0 0 auto; margin-top: 1px; width: 15px; height: 15px; }

      /* Infos pratiques */
      .grid-info { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
      .info-card { background: #fff; border: 1px solid #eee; border-radius: 14px; padding: 20px 16px; text-align: center; }
      .info-ic { color: #C81E2C; margin-bottom: 10px; }
      .info-label { font-size: 10.5px; letter-spacing: 1px; text-transform: uppercase; color: #9a9a9a; margin-bottom: 4px; font-weight: 600; }
      .info-value { font-size: 13.5px; font-weight: 600; color: #1A1A1A; line-height: 1.4; }

      /* Avantages */
      .grid-avantages { display: flex; flex-direction: column; gap: 14px; max-width: 520px; margin: 0 auto; }
      .avantage-item { display: flex; align-items: center; gap: 14px; background: #fff; border: 1px solid #eee; border-radius: 14px; padding: 14px 16px; font-size: 14px; font-weight: 600; }

      /* Promo */
      .promo { background: linear-gradient(135deg, #C81E2C, #8F0F1C); color: #fff; text-align: center; }
      .promo-inner { display: flex; flex-direction: column; align-items: center; gap: 6px; }
      .promo-title { font-family: 'Sora', sans-serif; font-size: 24px; font-weight: 800; margin: 10px 0 18px; }
      .promo-tags { display: flex; flex-wrap: wrap; gap: 10px; justify-content: center; }
      .promo-tag { background: rgba(255,255,255,0.15); border: 1px solid rgba(255,255,255,0.4); border-radius: 20px; padding: 7px 16px; font-size: 12.5px; font-weight: 600; }

      /* Tarifs */
      .grid-tarifs { display: flex; flex-direction: column; gap: 10px; margin-bottom: 28px; }
      .tarif-row { display: flex; align-items: center; justify-content: space-between; background: #fff; border: 1px solid #eee; border-radius: 12px; padding: 12px 16px; }
      .tarif-left { display: flex; align-items: center; gap: 12px; font-size: 13.5px; font-weight: 600; }
      .tarif-price { font-family: 'Sora', sans-serif; font-weight: 800; font-size: 14px; color: #C81E2C; }
      .total-box { display: flex; flex-direction: column; gap: 16px; background: #1A1A1A; color: #fff; border-radius: 16px; padding: 24px; }
      .total-divider { height: 1px; background: rgba(255,255,255,0.15); }
      .total-label { font-size: 11px; letter-spacing: 1px; text-transform: uppercase; color: #bbb; margin-bottom: 4px; }
      .total-value { font-family: 'Sora', sans-serif; font-size: 24px; font-weight: 800; }

      /* CTA */
      .cta-section { background: #C81E2C; color: #fff; text-align: center; }
      .cta-inner { display: flex; flex-direction: column; align-items: center; gap: 16px; }
      .cta-title { font-family: 'Sora', sans-serif; font-size: 28px; font-weight: 800; }
      .cta-desc { font-size: 14.5px; max-width: 440px; opacity: 0.95; line-height: 1.6; margin-bottom: 6px; }

      /* Contact */
      .contact-grid { display: flex; flex-direction: column; gap: 26px; }
      .contact-info { display: flex; flex-direction: column; gap: 18px; }
      .contact-item { display: flex; align-items: flex-start; gap: 14px; }
      .map-wrap { width: 100%; height: 260px; border-radius: 16px; overflow: hidden; border: 1px solid #eee; }

      /* Footer */
      .footer { background: #1A1A1A; color: #fff; padding: 40px 0 0; }
      .footer-inner { display: flex; flex-direction: column; gap: 24px; padding-bottom: 30px; }
      .footer-logo { color: #fff; }
      .footer-logo span { color: #C81E2C; }
      .footer-slogan { font-size: 12.5px; color: #aaa; margin-top: 4px; }
      .footer-links { display: flex; flex-wrap: wrap; gap: 16px; }
      .footer-links button { font-size: 13px; color: #ddd; font-weight: 500; }
      .footer-bottom { border-top: 1px solid rgba(255,255,255,0.1); text-align: center; padding: 16px 20px; font-size: 11.5px; color: #888; }

      /* WhatsApp flottant */
      .floating-wa { position: fixed; bottom: 20px; right: 20px; width: 56px; height: 56px; border-radius: 50%; background: #25D366; color: #fff; display: flex; align-items: center; justify-content: center; box-shadow: 0 4px 14px rgba(0,0,0,0.25); z-index: 45; }

      /* Responsive */
      @media (min-width: 640px) {
        .grid-modules { grid-template-columns: 1fr 1fr; }
        .grid-info { grid-template-columns: repeat(3, 1fr); }
        .contact-grid { flex-direction: row; }
        .contact-info { flex: 1; }
        .map-wrap { flex: 1; height: auto; }
      }
      @media (min-width: 860px) {
        .nav-desktop { display: flex; }
        .menu-btn { display: none; }
        .hero { flex-direction: row; text-align: left; padding-top: 70px; padding-bottom: 70px; }
        .hero-text { flex: 1; }
        .hero h1 { font-size: 46px; }
        .hero-desc { margin-left: 0; }
        .hero-actions { justify-content: flex-start; }
        .hero-visual { flex: 1; max-width: 380px; }
        .grid-modules { grid-template-columns: repeat(3, 1fr); }
        .footer-inner { flex-direction: row; justify-content: space-between; align-items: flex-start; }
      }
    `}</style>
  );
}
