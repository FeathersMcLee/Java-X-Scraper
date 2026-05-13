# Java-X-Scraper
Program that extracts links into a .txt from X.com bookmarks. Paste code into inspector console.

Tutorial
# X Bookmark Downloader

Download all media from X bookmarks using:
- browser console scraper
- gallery-dl
- exported cookies

---

# Install gallery-dl

Download latest Windows executable from:

https://codeberg.org/mikf/gallery-dl/releases

Place executable here:

```text
D:\Programs\ytdlp\gallery-dl.exe
```

Verify install:

```bat
"D:\Programs\ytdlp\gallery-dl.exe" --version
```

---

# Export X Cookies

Install browser extension:

```text
Get cookies.txt LOCALLY
```

Export cookies for:

```text
x.com
```

Save as:

```text
D:\Programs\ytdlp\cookies.txt
```

IMPORTANT:
- Never share cookies.txt
- It contains your login session

---

# Scrape Bookmark Links

Open:

```text
https://x.com/i/bookmarks
```

Open Developer Console:

```text
F12
```

Paste into Console:

```js
(async () => {
  const sleep = (ms) => new Promise(r => setTimeout(r, ms));
  const seen = new Set();

  function harvest() {
    document.querySelectorAll('a[href*="/status/"]').forEach(a => {
      let href = a.href || a.getAttribute("href");
      if (!href) return;

      if (href.startsWith("/")) {
        href = location.origin + href;
      }

      const m = href.match(
        /^https:\/\/(?:x|twitter)\.com\/[^\/]+\/status\/\d+/
      );

      if (m) {
        seen.add(m[0]);
      }
    });
  }

  let idle = 0;
  let lastCount = 0;

  for (let i = 0; i < 500; i++) {
    harvest();

    window.scrollTo(0, document.body.scrollHeight);

    await sleep(1200);

    if (seen.size === lastCount) {
      idle++;
    } else {
      idle = 0;
    }

    lastCount = seen.size;

    console.log(`Found ${seen.size} bookmark links...`);

    if (idle >= 8) {
      break;
    }
  }

  const text = [...seen].join("\n");

  const blob = new Blob([text], {
    type: "text/plain"
  });

  const a = document.createElement("a");

  a.href = URL.createObjectURL(blob);

  a.download = "bookmarks.txt";

  a.click();

  console.log(`Done. Saved ${seen.size} links.`);
})();
```

Move downloaded file to:

```text
D:\Programs\ytdlp\bookmarks.txt
```

---

# Download Media

Run in Command Prompt:

```bat
"D:\Programs\ytdlp\gallery-dl.exe" --cookies "D:\Programs\ytdlp\cookies.txt" --download-archive "D:\Morememes\X\archive.txt" -o filename="{author[name]}_{tweet_id}_{num}.{extension}" -D "D:\Morememes\X" -i "D:\Programs\ytdlp\bookmarks.txt"
```

Downloads media to:

```text
D:\Morememes\X
```

---

# Duplicate Prevention

Do NOT delete:

```text
D:\Morememes\X\archive.txt
```

This prevents re-downloading files.

---

# Updating gallery-dl

If errors appear like:

```text
ondemand.s.a.js 404
```

Update gallery-dl from:

https://codeberg.org/mikf/gallery-dl/releases

Replace old executable:

```text
D:\Programs\ytdlp\gallery-dl.exe
```

---

# Notes

- yt-dlp works better for videos
- gallery-dl works better for images/galleries
- X changes backend/API often
- old gallery-dl versions break frequently

---

# Useful Paths

```text
gallery-dl:
D:\Programs\ytdlp\gallery-dl.exe

cookies:
D:\Programs\ytdlp\cookies.txt

bookmark links:
D:\Programs\ytdlp\bookmarks.txt

downloads:
D:\Morememes\X
```
