<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>WhatsApp öffnen</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f7f7f8;
      color: #222;
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      margin: 0;
    }
    .card {
      background: white;
      padding: 24px;
      border-radius: 12px;
      box-shadow: 0 6px 18px rgba(0,0,0,0.06);
      width: 360px;
      max-width: calc(100% - 32px);
    }
    h1 { font-size: 20px; margin: 0 0 12px; }
    label { display:block; font-size: 13px; margin-top: 10px; }
    input[type="text"], textarea {
      width: 100%;
      padding: 10px;
      margin-top: 6px;
      border-radius: 8px;
      border: 1px solid #ddd;
      box-sizing: border-box;
      font-size: 14px;
    }
    textarea { resize: vertical; min-height: 80px; }
    .controls { margin-top: 14px; display:flex; gap:8px; }
    button {
      flex:1;
      padding: 10px;
      border: none;
      border-radius: 8px;
      background: #25D366;
      color: white;
      font-weight: 600;
      cursor: pointer;
      font-size: 15px;
    }
    button.secondary {
      background: #0078d7;
    }
    .note { font-size: 13px; color: #666; margin-top: 10px; }
  </style>
</head>
<body>
  <div class="card">
    <h1>WhatsApp öffnen</h1>

    <label for="phone">Telefonnummer (international, ohne +, z.B. 491701234567)</label>
    <input id="phone" type="text" placeholder="z. B. 491701234567" inputmode="numeric" />

    <label for="msg">Vorgefertigte Nachricht (optional)</label>
    <textarea id="msg" placeholder="Hallo! Ich möchte mit dir sprechen..."></textarea>

    <div class="controls">
      <button id="openBtn">Öffnen</button>
      <button id="openInSame" class="secondary">Im selben Tab öffnen</button>
    </div>

    <div class="note">
      Hinweis: Manche Geräte benutzen automatisch WhatsApp-App (Mobil). Auf Desktop öffnet sich WhatsApp Web.
    </div>
  </div>

  <script>
    function sanitizePhone(raw) {
      if (!raw) return '';
      // Nur Ziffern behalten
      const digits = raw.replace(/\D/g, '');
      return digits;
    }

    function buildWhatsAppUrl(phone, text, preferSameTab=false) {
      const encodedText = encodeURIComponent(text || '');
      const isMobile = /Android|iPhone|iPad|iPod|Mobile/i.test(navigator.userAgent);
      // Für mobile Geräte die "api" URL nutzen (öffnet App wenn vorhanden)
      if (isMobile) {
        // api.whatsapp.com funktioniert zuverlässig auf mobilen Geräten
        return `https://api.whatsapp.com/send?phone=${phone}${encodedText ? '&text=' + encodedText : ''}`;
      } else {
        // Desktop: web.whatsapp.com
        return `https://web.whatsapp.com/send?phone=${phone}${encodedText ? '&text=' + encodedText : ''}`;
      }
    }

    function openWhatsApp(inSameTab=false) {
      const rawPhone = document.getElementById('phone').value.trim();
      const message = document.getElementById('msg').value.trim();

      const phone = sanitizePhone(rawPhone);
      if (!phone) {
        alert('Bitte gib eine Telefonnummer im internationalen Format ein (ohne +).');
        return;
      }

      // Erstelle URL
      const url = buildWhatsAppUrl(phone, message);

      if (inSameTab) {
        window.location.href = url;
      } else {
        window.open(url, '_blank', 'noopener,noreferrer');
      }
    }

    document.getElementById('openBtn').addEventListener('click', function () {
      openWhatsApp(false);
    });

    document.getElementById('openInSame').addEventListener('click', function () {
      openWhatsApp(true);
    });

    // Enter im Eingabefeld -> Öffnen
    document.getElementById('phone').addEventListener('keydown', function(e) {
      if (e.key === 'Enter') { e.preventDefault(); openWhatsApp(false); }
    });
  </script>
</body>
</html>
