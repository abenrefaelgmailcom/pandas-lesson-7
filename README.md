# pandas-lesson-7
pandas lesson 7

🔹 נושא מרכזי: עיבוד נתונים עם תאריכים וסידורים (Series), ביצוע ניקוי נתונים, ושימוש בפונקציית resample לזיהוי מגמות לאורך זמן.
✅ שלב 1: ניקוי טקסט ב-Series
דוגמה:
my_array = ['ben ', ' hello', ' chair']
my_data = pd.Series(my_array)

שיטות לניקוי:
שימוש בשרשור של פקודות:

my_data.str.replace(' ', '').str.strip().str.capitalize()

📌 חסרון: פחות יעיל – מבצע פעולות רבות על כל תא.

שימוש בפונקציה מותאמת אישית:

def clean(string):
    string = string.replace(' ', '').strip().capitalize()
    return string

my_data.apply(clean)


✅ יתרון: פעולה אחת בלבד – ביצועים טובים יותר.

✅ שלב 2: קריאת קובץ CSV עם תאריכים
beer = pd.read_csv("RetailSales_BeerWineLiquor.csv")

המרה לעמודת תאריכים:
beer['DATE'] = pd.to_datetime(beer['DATE'])

📌 אם התבנית מיוחדת (כמו "12--Dec--2000"), יש לציין את הפורמט:


pd.to_datetime(s, format="%d--%b--%Y")

✅ שלב 3: שימוש ב-resample – עיבוד לפי זמן
פעולות:
שינוי אינדקס לעמודת תאריך:


beer.set_index('DATE', inplace=True)
דגימה מחדש לפי שנה ('A' = Annual) עם מקסימום בכל שנה:


beer.resample('A').max().head()
ניתן גם לבצע sum(), mean(), min() וכו'.


✅ שלב 4: resample עם פעולות סטטיסטיות מרובות

beer.resample(rule='A').agg({
    "Total_Sales": ["max", "min", "mean", "std"]
})
אפשר גם לרדת לרמת רבעון:

beer.resample('Q').agg({...})
📌 resample() מאפשר "קיבוץ לפי זמן" → ואז הפעלת פונקציות כמו sum, mean, max, min, std (סטיית תקן), וכו'.

🕓 קודים של תדירויות בזמן עבור resample
קוד	הסבר
'A'	סוף שנה (Annual – Dec 31)
'AS'	תחילת שנה (Jan 1)
'Q'	סוף רבעון
'QS'	תחילת רבעון
'M'	סוף חודש
'MS'	תחילת חודש
'W'	סוף שבוע
'W-SUN'	סוף שבוע ביום ראשון
'W-MON'	סוף שבוע ביום שני
'D'	יומי
'H'	שעתית
'T' או 'min'	דקות
'S'	שניות
'L'	מילי-שנייה
'U'	מיקרו-שנייה
'N'	ננו-שנייה
✅ שלב 5: טיפול בתאריכים עם תבניות שונות
דוגמה 1: תבנית אחת ברורה

s = pd.Series(["12--Dec--2000"])
pd.to_datetime(s, format="%d--%b--%Y")
📌 חובה לציין פורמט אם התבנית לא סטנדרטית.

דוגמה 2: תבניות מעורבות בסדרה

myser2 = pd.Series(['Nov 3, 1990', '2000-01-15', None])
🔧 פתרון: פונקציה שמנסה כמה פורמטים שונים:


formats = ['%b %d, %Y', '%Y-%m-%d']

def parse_multiple_formats(date):
    for fmt in formats:
        try:
            return pd.to_datetime(date, format=fmt)
        except ValueError:
            continue
    return pd.NaT

myser2 = myser2.apply(parse_multiple_formats)
📌 מחזיר NaT אם אין פורמט מתאים.

הנה סיכום החלק האחרון של שיעור 7 במחברת Pandas:

✅ שלב 6: שימוש ב-pivot_table לניתוח נתונים
יצירת טבלת נתונים:

data = {
    'Date': [...],
    'Product': [...],
    'Sales': [...]
}
df = pd.DataFrame(data)
הטבלה כוללת תאריכים, שמות מוצרים (Apples, Oranges) וסכום מכירות בכל יום.

🧩 שימוש ב-pivot_table – טבלאות ציר
דוגמה בסיסית:

pd.pivot_table(df, columns='Product', values='Sales', aggfunc='sum')
📌 מחשבת את סך המכירות של כל מוצר לאורך כל התקופה.

דוגמה מתקדמת: ריבוי פונקציות אגרגציה
python
Copy
Edit
pd.pivot_table(
    df,
    columns='Product',
    values='Sales',
    aggfunc=['sum', 'mean', 'max']
)
📌 מאפשר לראות סיכום, ממוצע ומקסימום לכל מוצר.

דוגמה מתקדמת עם אינדקס לפי תאריך:
python
Copy
Edit
pivot = pd.pivot_table(
    df,
    index='Date',
    columns='Product',
    values='Sales',
    aggfunc=[
        lambda x: sum(x) / 2,
        lambda x: max(x) / 2,
        'max'
    ]
)
📌 כאן מבוצעות פעולות מותאמות אישית:

חצי מהסכום
חצי מהמקסימום
וגם מקסימום רגיל
✏️ שינוי שמות העמודות בטבלת Pivot
python
Copy
Edit
old = pivot.columns[5]
pivot = pivot.rename(columns={old: ('half-sum', 'Apple')})
📌 הסבר: לפעמים התוצאה כוללת שמות מורכבים לעמודות בגלל הפונקציות → ניתן לשנות שם עמודה לפי מיקום או שם.

🟩 סיכום סופי של שיעור 7:
מה למדת?
איך לנקות מחרוזות ב-Series
איך להמיר תאריכים בפורמטים שונים
איך להשתמש ב-resample לניתוח מגמות לאורך זמן
שימוש מתקדם ב-pivot_table כולל פונקציות מותאמות אישית
איך לשנות שמות עמודות בפיבוט




🧠 מילון מונחים חשובים:
מונח	הסבר
pd.Series	אובייקט חד-ממדי של פנדס – כמו רשימה עם אינדקס
apply()	מפעיל פונקציה על כל ערך בסדרה
str.strip()	מסיר רווחים מיותרים בהתחלה ובסוף
capitalize()	הופך את האות הראשונה לגדולה והשאר לקטנות
pd.to_datetime()	ממיר מחרוזות תאריך לפורמט תאריך של פייתון
set_index()	מגדיר עמודה מסוימת כאינדקס (כאן – תאריך)
resample()	דגימה מחדש של נתונים לפי תאריכים (שנה/חודש/רבעון וכו')
🟩 סיכום כללי:
בשיעור הזה למדת:

איך לנקות טקסטים ביעילות ב-Series
איך להמיר מחרוזות לתאריכים
איך לבצע פעולות אגרגציה לפי זמן (כמו לפי שנה)
איך לעבוד עם אינדקס של תאריכים בעזרת resample
אם תרצה – אפשר להכין לך גם תרגילים בנושא הזה 😄

נמשיך את הסיכום של שיעור 7 ב-Pandas מהקטע הבא במחברת:

