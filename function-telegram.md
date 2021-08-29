# Function Kirim Pesan ke Telegram

Jika anda berencana untuk menggunakan banyak script di MikroTik dan mengirimkan hasilnya ke Telegram. Tidak enak untuk menulis url "https://api.telegram.org/blablabla" di setiap script, cara terbaik adalah menaruh fuction "kirim pesan ke Telegram" di script terpisah dan kita tinggal memanggil skirp ini.

---

Script menggunakan markup html, opsi markup yang digunakan Telegram dapat dibaca [disini](https://core.telegram.org/bots/api#formatting-options).

Script menerima variabel __MessageText__, yang berisi teks pesan. __MessageText__ tidak boleh kosong.

Karena script akan dieksekusi dari script lain, permissions script tidak diperlukan.

```
[System] -> [Scripts] -> [+] -> [Name: tg_SendMessage] -> [Tidak memerlukan Permissions]
```

### Script code :
```mikrotik
:local BotToken "TOKEN-BOT-KAMU";
:local ChatID "CHAT-ID-KAMU";
:local ParseMode "html";
:local DisableWebPagePreview True;
:local SendText $MessageText;

:local tgUrl "https://api.telegram.org/bot$BotToken/sendMessage?chat_id=$ChatID&text=$SendText&parse_mode=$ParseMode&disable_web_page_preview=$DisableWebPagePreview";

/tool fetch http-method=get url=$tgUrl;

:log info "Send Telegram Message: $MessageText";
```
---

### Test memanggil script "tg_SendMessage" dari script lain :
Sekarang kita akan test apakah script "tg_SendMessage" mau berkerja apa tidak?, Kita test dengan script ini.

```mikrotik
:local DeviceName [/system identity get name];

:local MessageText "\F0\9F\9F\A2 Mikrotik: $DeviceName%0A<b>bold</b>%0A<i>italic</i>%0A<u>underline</u>%0A<s>strikethrough</s>%0A<code>inline fixed-width code</code>%0A<pre>pre-formatted fixed-width code block</pre>";

:local SendTelegramMessage [:parse [/system script get tg_SendMessage source]];

$SendTelegramMessage MessageText=$MessageText;
```
Jika saat memanggil skrip, Anda menerima kesalahan __syntax error__ atau __failure: closing connection: <400 Bad Request> error>__, masalahnya mungkin dalam penggunaan karakter layanan MikroTik dalam teks pesan, untuk verifikasi coba kirim teks biasa tanpa karakter khusus atau karakter markup, dalam bahasa Inggris.






