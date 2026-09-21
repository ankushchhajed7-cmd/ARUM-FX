# Aurum FX Signals

Mobile par install hone wala signal app (PWA). Isme 10 pairs ke BUY / SELL / HOLD signals ek saath dikhte hain. Paper trading aur MT5 par direct trade, dono ki suvidha hai. Main trade aap manually lete hain, jabki correlation hedge, basket close aur daily limits apne aap chalte hain.

## Files

| File | Kaam |
|---|---|
| `index.html` | Poora app (dashboard, trades, AI, settings) |
| `manifest.json`, `sw.js`, `icon-*.png` | Mobile par install aur offline support ke liye |
| `SignalBridgeEA.mq5` | MT5 bridge EA: signals bhejta hai, app ke trades lagata hai, hedge apne aap karta hai |
| `database.rules.json` | Firebase security rules |

## 1. GitHub Pages par chalana

1. GitHub par naya **public** repo banayein, jaise `aurum-fx`.
2. Upar di gayi saari files (`.mq5` ke alawa) repo me upload karein.
3. Repo me **Settings → Pages → Branch: main → / (root) → Save** karein.
4. 1–2 minute baad link milega: `https://USERNAME.github.io/aurum-fx/`

⚠️ **Koi bhi API key code me ya GitHub par kabhi na daalein.** Google public GitHub par dikhi Gemini key apne aap band kar deta hai. Aapki key isi wajah se "remove" ho rahi thi. Is app me keys sirf Settings screen me daalni hain.

## 2. Mobile par install

- **Android (Chrome):** link kholein, phir Settings me "App install karein" dabayein, ya menu se "Install app" chunein.
- **iPhone (Safari):** Share button dabayein, phir "Add to Home Screen" chunein.

## 3. Firebase setup (live mode aur backup ke liye)

1. Firebase console me **Realtime Database** banayein. Uska URL kuch aisa hoga: `https://xxx-default-rtdb.firebaseio.com`
2. **Rules** tab me `database.rules.json` ka content paste karke Publish karein.
3. App ke Settings me Firebase URL daalein, "Naya Bridge ID" dabayein, phir Save karein.
4. Yahi Bridge ID MT5 EA me bhi daalni hai. Is ID ko password ki tarah secret rakhein.

## 4. MT5 bridge EA

1. `SignalBridgeEA.mq5` ko `MQL5/Experts` folder me daalein aur MetaEditor me compile (F7) karein.
2. MT5 me **Tools → Options → Expert Advisors** kholein. "Allow WebRequest for listed URL" tick karein aur apna Firebase URL add karein.
3. EA ko kisi ek chart par lagayein. Inputs me Firebase URL aur Bridge ID daalein, phir Algo Trading ON karein.
4. App me Mode ko "MT5 live" karein. Upar "Data: MT5 live" dikhna chahiye.

Dhyan dein:
- Live mode sirf tab tak kaam karta hai jab tak PC ya VPS par MT5 aur yeh EA chal rahe hon.
- Purana auto-trading EA (v3) aur yeh bridge EA ek hi account par ek saath na chalayein.

## 5. Kaise kaam karta hai

- **Signal:** M5 EMA50 se trend dekha jata hai, M1 EMA9/21 crossover (pichhle 3 bar ke andar) aur RSI se confirmation liya jata hai. Yeh bilkul wahi strategy hai jo EA me hai.
- **Main trade:** Aap Buy ya Sell dabate hain. Ek time par sirf ek main trade khulta hai.
- **Hedge:** Main trade ka net loss $1 hote hi correlated pair par hedge apne aap khulta hai.
- **Exit:**
  - Akela main trade $0.20 net par close hota hai. Agar profit $0.15 tak jaakar wapas $0.10 par aaye, to wahin lock hokar close hota hai.
  - Main + hedge ka combined net $0.10 hone par dono close hote hain.
  - Main + hedge ka combined loss $5 hone par dono close hote hain.
- **Daily limits:** $5 profit par naye trades band ho jaate hain. $10 loss par saare trades close hokar trading band ho jaati hai. Har pair par din me max 10 trades.
- **Time:** 7 AM se 4 PM IST.
- **News filter:** MT5 bridge se aata hai. AI tab me "Aaj ki news check" sirf salah deta hai.

## 6. Twelve Data ki limit (zaroori)

Free plan me sirf 8 calls per minute aur 800 calls per din milti hain. App har call me ek pair ke 500 M1 candles leta hai, isliye free plan par:
- har pair lagbhag 75 second me ek baar update hota hai, aur
- 800 calls lagbhag 1.5–2 ghante me khatam ho jaati hain.

Poore din M1 scalping ke liye MT5 bridge data (free aur real-time) sabse behtar hai. App bridge online hote hi apne aap usi ka data use karta hai. Twelve Data ko backup ki tarah rakhein, ya paid plan lein.

## 7. Backup

- App roz khulte hi Firebase me `backup/YYYY-MM-DD` ke naam se backup leta hai.
- Naye phone par Settings me Firebase URL aur Bridge ID likhkar "Firebase se restore" dabayein.
- "Backup file download" se ek local copy bhi rakh sakte hain.
