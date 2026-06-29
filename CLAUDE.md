# CLAUDE.md — אתר "חומר גלם"

קובץ הקשר (context) לפרויקט. קלוד קוד קורא אותו אוטומטית בתחילת כל סשן.
כל ההוראות והטקסטים בעברית. האתר RTL.

---

## מה זה הפרויקט

אתר תדמית (landing page) לסטודיו **חומר גלם — בית לשפת הגוף**, בהנחיית **נטע רוזנצוויג**.
מרחב למחול, תנועה, גוף ויצירה. קהל היעד: נשים ונערות.

- **דומיין:** chomergelem.com
- **Cloudflare Pages:** chomergelem.pages.dev (auto-deploy מ-main)
- **ריפו GitHub:** https://github.com/yoss613/chomerGelem
- **אינסטגרם:** [@homer.gelem](https://instagram.com/homer.gelem)
- **טון:** רך, אישי, פואטי, אורגני — לא מכירתי. הגוף כ"חומר גלם" שמקבל צורה.

---

## מצב נוכחי

קיים קובץ יחיד **`index.html`** — אתר עובד, בדומיין chomergelem.com.
HTML + CSS + JS, הכל inline בקובץ אחד. אין build step, אין תלויות חיצוניות מלבד Google Fonts.

### מבנה הסקשנים (anchors)
| Anchor | סקשן | תוכן |
|---|---|---|
| `#top` | הירו | לוגו (`assets/chomergelem-logo-circle.png`) + SVG רקדנית + CTA |
| `#about` | המקום | פילוסופיה בקול של נטע + לוגו כותנה (`assets/chomergelem-logo-cotton.png`) |
| `#classes` | שיעורים | 3 כרטיסים: מחול מודרני לנערות / תנועה וגוף לנשים / סדנאות |
| `#show` | המופע | רצועת "מקום לאהבה" (מופע סיום שנה) |
| `#gallery` | גלריה | 8 ריבועים — **placeholders**, ממתינים לתמונות אמיתיות |
| `#contact` | צרו קשר | אינסטגרם / טלפון / מייל + לוגו |

### טכני
- **שפה/כיוון:** `<html lang="he" dir="rtl">`
- **JS (vanilla):** תפריט המבורגר למובייל + `IntersectionObserver` לאנימציות חשיפה (`.reveal` → `.in`)
- **רספונסיב:** breakpoints ב-880px ו-540px.
- **נגישות:** מכבד `prefers-reduced-motion`, focus מהמקלדת, alt/aria-hidden לאיורים.

### קבצים בריפו
```
index.html
CLAUDE.md
assets/
  chomergelem-logo-circle.png   ← לוגו עיגול (הירו + צור קשר)
  chomergelem-logo-cotton.png   ← לוגו כותנה (סקשן אודות)
```

---

## שפת המותג (Design Tokens)

מוגדרים כ-CSS variables ב-`:root` בתוך `index.html`. **לא לשנות בלי סיבה.**

```css
--taupe:#b6a591;   /* רקע חמרה ראשי (הירו/צור קשר) */
--paper:#f3ecdf;   /* נייר/קרם – רקע כללי */
--green:#3e4a33;   /* ירוק יער – צבע הלוגו והכותרות */
--coffee:#7b5e45;  /* חום קפה – אקסנט */
--rose:#bd928a;    /* ורוד עתיק – אקסנט עדין */
--ink:#34302a;     /* טקסט */
```

### טיפוגרפיה (Google Fonts)
- **כותרות:** `Suez One` (עברית, חזק וטקסטורלי)
- **גוף:** `Assistant` (עברית, נקי וקריא)
- **אקסנט פואטי:** `Amatic SC` (לשורות קצרות בלבד, class `.eyebrow`)

---

## איך מעלים שינויים

הריפו **אינו** git clone מקומי — מעלים דרך GitHub REST API:

```python
import urllib.request, json, base64

TOKEN = "..."  # PAT עם Contents: Read and write על yoss613/chomerGelem
BASE = "https://api.github.com/repos/yoss613/chomerGelem/contents"
HEADERS = {"Authorization": f"Bearer {TOKEN}", "Content-Type": "application/json"}

def push_file(path, local_path, message):
    req = urllib.request.Request(f"{BASE}/{path}", headers=HEADERS)
    try:
        with urllib.request.urlopen(req) as r:
            sha = json.load(r)["sha"]
    except:
        sha = None
    with open(local_path, "rb") as f:
        content = base64.b64encode(f.read()).decode()
    body = {"message": message, "content": content}
    if sha:
        body["sha"] = sha
    req = urllib.request.Request(f"{BASE}/{path}",
          data=json.dumps(body).encode(), headers=HEADERS, method="PUT")
    with urllib.request.urlopen(req) as r:
        json.load(r)
        print(f"OK: {path}")
```

**Cloudflare Pages** מבצע deploy אוטומטי על כל push ל-main.

---

## משימות פתוחות (TODO)

1. **תמונות** — להחליף 8 placeholders בגלריה בצילומים אמיתיים. כשיגיעו — לשמור ב-`assets/` ולעדכן `index.html`.
2. **פרטי קשר** — להחליף placeholders: טלפון (`tel:+972...`) + מייל.
3. **טקסטי שיעורים** — ימים/שעות/גיל כתובים כהשערה. נטע צריכה לאשר.
4. **קישור הרשמה** — כפתור וואטסאפ ישיר או טופס.

---

## כללי עבודה

- טקסטים בעברית, בטון של נטע (רך, פואטי, לא מכירתי).
- לא להמציא נתונים על שיעורים/מחירים — לסמן TODO ולשאול.
- לשמור על פלטת הצבעים והפונטים.
- לפני שינוי גדול — להציג הצעה קצרה תחילה.
