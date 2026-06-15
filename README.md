# Hisobot-generatori-template-literallar-bilan-
const htmlSafe = (strings, ...values) => {
  return strings.reduce((acc, str, i) => {
    const value = values[i] !== undefined ? String(values[i])
      .replaceAll('&', '&amp;')
      .replaceAll('<', '&lt;')
      .replaceAll('>', '&gt;')
      .replaceAll('"', '&quot;')
      .replaceAll("'", '&#39;') : '';
    return acc + str + value;
  }, '');
};
// Dastlabki ma'lumotlar
const transactions = [
  { id: "TXN-001", user: "Ali Valiyev <ali@it.uz>", amount: 12500000, status: "Muvaffaqiyatli", category: "Kredit" },
  { id: "TXN-002", user: "Zuhra Shirova", amount: 450000, status: "Kutilmoqda", category: "To'lov" },
  { id: "TXN-003", user: "Olim Karimov <admin>", amount: 32000000, status: "Muvaffaqiyatli", category: "Kredit" },
  { id: "TXN-004", user: "Madina Umarova", amount: 1850000, status: "Rad etildi", category: "To'lov" }
];

// Summalarni o'zbek so'mida formatlash uchun helper
const formatUzS = amount => amount.toLocaleString('uz-UZ', { style: 'currency', currency: 'UZS' });

// ---------------------------------------------------------
// TYPE 1: JADVAL HISOBOTI (padStart/padEnd va String metodlari)
// ---------------------------------------------------------
const generateTableReport = (data || []) => {
  const headerId = "ID".padEnd(10);
  const headerUser = "Mijoz".padEnd(20);
  const headerCategory = "Tur".padEnd(12);
  const headerAmount = "Summa".padStart(18);
  const separator = "-".repeat(60);

  const rows = data.map(({ id, user, category, amount }) => {
    // split va join orqali foydalanuvchi nomidan email qismini tozalash
    const cleanUser = user.split(" <")[0].trim().slice(0, 18);
    return `${id.padEnd(10)}${cleanUser.padEnd(20)}${category.padEnd(12)}${formatUzS(amount).padStart(18)}`;
  }).join("\n");

  return `\n${" JADVAL KO'RINISHIDAGI HISOBOT ".padStart(45, "=").padEnd(60, "=")}\n${headerId}${headerUser}${headerCategory}${headerAmount}\n${separator}\n${rows}\n${separator}`;
};

// ---------------------------------------------------------
// TYPE 2: SUMMARY (QISQA STATISTIKA) HISOBOTI
// ---------------------------------------------------------
const generateSummaryReport = (data || []) => {
  const totalAmount = data.reduce((sum, item) => sum + item.amount, 0);
  const successCount = data.filter(item => item.status.includes("Muvaffaqiyatli")).length;
  const pendingCount = data.filter(item => item.status.includes("Kutilmoqda")).length;

  return `
=================== UMUMIY STATISTIKA ===================
Jami tranzaksiyalar soni : ${data.length} ta
Muvaffaqiyatli yakunlandi: ${successCount} ta
Kutilayotgan jarayonlar  : ${pendingCount} ta
Umumiy aylanma hajmi     : ${formatUzS(totalAmount)}
=========================================================
`.trim();
};

// ---------------------------------------------------------
// TYPE 3: DETAILED (BATAFSIL / XAT SHAKLIDAGI) HISOBOT
// ---------------------------------------------------------
const generateDetailedReport = (data || []) => {
  return data.map(({ id, user, amount, status, category }) => {
    const cleanUser = user.includes("<") ? user.split("<")[0].trim() : user;
    
    // Ko'p qatorli rasmiy xat ko'rinishidagi Template Literal
    return `
Hurmatli nazoratchi,

${id} raqamli tranzaksiya tafsilotlari quyidagicha:
- Mijoz: ${cleanUser}
- Yo'nalish: ${category} turi bo'yicha
- Moliyaviy miqdor: ${formatUzS(amount)}
- Joriy holat: Ushbu amaliyot tizimda [${status.toUpperCase()}] deb qayd etildi.

Xavfsizlik xizmati hisoboti yakunlandi.
---------------------------------------------------------`.trim();
  }).join("\n\n");
};
// 1. Jadval shaklidagi hisobotni chiqarish
console.log(generateTableReport(transactions));

// 2. Qisqa summary hisobotni chiqarish
console.log("\n", generateSummaryReport(transactions));

// 3. Batafsil xat ko'rinishidagi hisobotni chiqarish
console.log("\n=== BATAFSIL XAT SHAKLIDAGI HISOBOTLAR ===\n");
console.log(generateDetailedReport(transactions));

// 4. Tagged Template (HTML Escaping) Testi
const xavfliIsm = "<script>alert('Hacked')</script> Jamshid";
const xavfsizHTML = htmlSafe`<div>Foydalanuvchi: ${xavfliIsm}</div>`;
console.log("\n=== HTML ESCAPE TESTI ===");
console.log(xavfsizHTML);
