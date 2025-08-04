<!DOCTYPE html>
<html lang="ms">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>eAduan Kebersihan Sekolah</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      background-color: #f2f2f2;
    }
    .container {
      max-width: 500px;
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      margin: auto;
    }
    input, textarea, button {
      width: 100%;
      padding: 10px;
      margin-top: 10px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }
    button {
      background-color: #007bff;
      color: white;
      font-weight: bold;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>eAduan Kebersihan Sekolah</h2>
    <form id="aduanForm">
      <label for="nama">Nama Pengadu:</label>
      <input type="text" id="nama" name="nama" required>

      <label for="sekolah">Nama Sekolah:</label>
      <input type="text" id="sekolah" name="sekolah" required>

      <label for="aduan">Maklumat Aduan:</label>
      <textarea id="aduan" name="aduan" rows="4" required></textarea>

      <label for="gambar">Muat Naik Gambar (wajib):</label>
      <input type="file" id="gambar" name="gambar" accept="image/*" required capture>

      <button type="submit">Hantar Aduan</button>
    </form>
    <p id="status"></p>
  </div>

  <script>
    document.getElementById('aduanForm').addEventListener('submit', async function(e) {
      e.preventDefault();
      const form = e.target;
      const nama = form.nama.value;
      const sekolah = form.sekolah.value;
      const aduan = form.aduan.value;
      const gambar = form.gambar.files[0];
      const timestamp = new Date().toLocaleString();

      if (!gambar) return alert("Sila muat naik gambar kejadian.");

      // Simpan ke Google Sheets melalui Google Apps Script Web App
      const sheetPayload = {
        nama,
        sekolah,
        aduan,
        timestamp
      };

      try {
        const sheetResponse = await fetch('https://script.google.com/macros/s/PASTE_YOUR_SCRIPT_ID_HERE/exec', {
          method: 'POST',
          body: JSON.stringify(sheetPayload),
          headers: {
            'Content-Type': 'application/json'
          }
        });

        // Hantar ke Telegram (hanya teks; gambar tidak dihantar dalam versi ini)
        const telegramText = `📌 Aduan Kebersihan Sekolah\n👤 Pengadu: ${nama}\n🏫 Sekolah: ${sekolah}\n📝 Aduan: ${aduan}\n⏰ Masa: ${timestamp}`;
        await fetch(`https://api.telegram.org/botTELEGRAM_BOT_TOKEN/sendMessage`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            chat_id: 'TELEGRAM_CHAT_ID',
            text: telegramText
          })
        });

        document.getElementById('status').textContent = 'Aduan berjaya dihantar ke Telegram dan Google Sheets.';
        form.reset();
      } catch (error) {
        console.error('Ralat:', error);
        document.getElementById('status').textContent = 'Gagal menghantar. Sila cuba semula.';
      }
    });
  </script>
</body>
</html>
