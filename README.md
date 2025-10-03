// IraqiPoetryLibrary.jsx // مكتبة الشعر العراقي — مع صفحة تفاصيل القصيدة وتحميل PDF.

import React, { useMemo, useState } from 'react';

const themes = ['حب', 'وطن', 'حنين', 'حزن', 'فخر', 'طبيعة', 'حكمة']; const poets = ['شاعر مجهول', 'عبدالله', 'نورة', 'حيدر', 'سعاد', 'محمود', 'ليلى'];

const generateTitle = (i) => قصيدة عراقية #${i+1};

const generateExcerpt = (i) => { const t = themes[i % themes.length]; const templates = [ يا وطنًا في القلبِ لا يَفنى — نجوم الليلِ تحرسُ عُيونَنا., قُبلاتُ الصبا على خدِّ الريح — تكتب اسمَك على الرمالِ., أمشي وحدي بينَ الحقولِ — والعصافيرُ ترتّلُ سلامًا., غدًا نَصنعُ من الحلمِ قصرًا — ونضعُ عليهِ لحيَتنا نِعمَةً., أنحني أمامَ الشّمسِ كي لا أنسى — لونَ الاندفاعِ في عروقِ السّنين., أحملُ في صدري قصائدَ المطرِ — وأزرعُها في حديقةِ الصمود., يا من سرقَ الفجرَ من نافذةِ العمر — رُدّ عليّ لياليكَ بأمانٍ. ]; return ${templates[i % templates.length]} (موضوع: ${t}); };

const generateBody = (i) => { return هذه هي القصيدة الكاملة رقم ${i+1}.\n\n + سطور إضافية من الشعر العراقي...\n + قد تضع هنا النص الكامل للقصيدة الحقيقية.; };

const generateYear = (i) => 1900 + (i % 126);

const generatePoems = (count = 2000) => { return new Array(count).fill(null).map((_, i) => ({ id: i+1, title: generateTitle(i), poet: poets[i % poets.length], theme: themes[i % themes.length], year: generateYear(i), excerpt: generateExcerpt(i), body: generateBody(i), })); };

export default function IraqiPoetryLibrary() { const allPoems = useMemo(() => generatePoems(2000), []); const [query, setQuery] = useState(''); const [filterTheme, setFilterTheme] = useState('all'); const [page, setPage] = useState(1); const [selectedPoem, setSelectedPoem] = useState(null); const perPage = 18;

const filtered = useMemo(() => { let list = allPoems; if (query.trim()) { const q = query.trim(); list = list.filter(p => p.title.includes(q) || p.poet.includes(q) || p.excerpt.includes(q)); } if (filterTheme !== 'all') list = list.filter(p => p.theme === filterTheme); return list; }, [allPoems, query, filterTheme]);

const totalPages = Math.max(1, Math.ceil(filtered.length / perPage)); const visible = filtered.slice((page-1)perPage, pageperPage);

const downloadPDF = (poem) => { const content = عنوان: ${poem.title}\nشاعر: ${poem.poet}\nسنة: ${poem.year}\nموضوع: ${poem.theme}\n\n${poem.body}; const blob = new Blob([content], { type: 'application/pdf' }); const url = URL.createObjectURL(blob); const a = document.createElement('a'); a.href = url; a.download = ${poem.title}.pdf; a.click(); URL.revokeObjectURL(url); };

if (selectedPoem) { return ( <div className="min-h-screen bg-gradient-to-br from-pink-50 via-purple-50 to-indigo-50 p-6"> <div className="max-w-4xl mx-auto bg-white rounded-2xl shadow-lg p-6"> <h1 className="text-3xl font-bold text-indigo-800 mb-2">{selectedPoem.title}</h1> <p className="text-gray-600 mb-4">{selectedPoem.poet} · {selectedPoem.year} · {selectedPoem.theme}</p> <pre className="whitespace-pre-wrap leading-7 text-gray-800">{selectedPoem.body}</pre> <div className="mt-6 flex justify-between"> <button onClick={() => setSelectedPoem(null)} className="px-4 py-2 rounded-lg bg-indigo-500 text-white hover:bg-indigo-600 transition">⬅ رجوع</button> <button onClick={() => downloadPDF(selectedPoem)} className="px-4 py-2 rounded-lg bg-pink-500 text-white hover:bg-pink-600 transition">⬇ تحميل PDF</button> </div> </div> </div> ); }

return ( <div className="min-h-screen bg-gradient-to-br from-pink-50 via-purple-50 to-indigo-50 p-6"> <header className="max-w-6xl mx-auto mb-6"> <div className="flex flex-col md:flex-row items-start md:items-center justify-between gap-4"> <h1 className="text-4xl font-extrabold text-indigo-800">📖 مكتبة الشعر العراقي</h1> <button onClick={() => { setFilterTheme('all'); setQuery(''); setPage(1); }} className="px-4 py-2 rounded-lg bg-pink-500 text-white shadow hover:bg-pink-600 transition">إعادة تعيين</button> </div> <p className="mt-1 text-sm text-gray-600">يتضمن الموقع <strong>2000</strong> قصيدة عينة — استبدلها بقصائدك الحقيقية عند الحاجة.</p> </header>

<main className="max-w-6xl mx-auto">
    <section className="mb-6 grid grid-cols-1 md:grid-cols-3 gap-4 items-end">
      <div className="col-span-2 flex gap-3">
        <input value={query} onChange={(e)=>{setQuery(e.target.value); setPage(1);}} placeholder="🔍 ابحث عن عنوان، شاعر، أو بيت..." className="flex-1 p-3 rounded-lg border border-indigo-300 focus:outline-none focus:ring-2 focus:ring-indigo-400" />
        <select value={filterTheme} onChange={(e)=>{setFilterTheme(e.target.value); setPage(1);}} className="p-3 rounded-lg border border-indigo-300 focus:outline-none focus:ring-2 focus:ring-indigo-400">
          <option value="all">الموضوع: الكل</option>
          {themes.map(t => <option key={t} value={t}>{t}</option>)}
        </select>
      </div>

      <div className="flex justify-end">
        <div className="text-right">
          <div className="text-sm text-gray-500">القصائد المتاحة</div>
          <div className="font-medium text-indigo-700">{filtered.length.toLocaleString()} نتيجة</div>
        </div>
      </div>
    </section>

    <section>
      <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6">
        {visible.map(poem => (
          <article key={poem.id} className="bg-white rounded-2xl shadow-md hover:shadow-xl transition p-5 border-t-4 border-pink-400">
            <h3 className="font-semibold text-lg text-indigo-800">{poem.title}</h3>
            <p className="text-sm text-gray-500">{poem.poet} · {poem.year} · {poem.theme}</p>
            <p className="mt-3 text-justify leading-6 text-gray-700">{poem.excerpt}</p>
            <div className="mt-4 flex items-center justify-between">
              <button onClick={() => setSelectedPoem(poem)} className="px-3 py-1 rounded-lg bg-indigo-500 text-white hover:bg-indigo-600 transition">اقرأ المزيد</button>
              <div className="text-sm text-gray-400"># {poem.id}</div>
            </div>
          </article>
        ))}
      </div>

      <div className="mt-8 flex items-center justify-between">
        <div>
          <button onClick={() => setPage(p => Math.max(1, p-1))} className="px-3 py-1 rounded border bg-white hover:bg-gray-100 transition mr-2">السابق</button>
          <button onClick={() => setPage(p => Math.min(totalPages, p+1))} className="px-3 py-1 rounded border bg-white hover:bg-gray-100 transition">التالي</button>
        </div>
        <div className="text-sm text-gray-600">صفحة {page} من {totalPages}</div>
      </div>
    </section>
  </main>

  <footer className="max-w-6xl mx-auto mt-12 text-center text-gray-500 text-sm">© {new Date().getFullYear()} مكتبة الشعر العراقي — تصميم تجريبي بألوان عصرية</footer>
</div>

); }

# -
يههي
