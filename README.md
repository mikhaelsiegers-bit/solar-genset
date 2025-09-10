<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kalkulator & Monitoring Genset</title>
    <!-- Memuat Tailwind CSS untuk styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Memuat font Inter dari Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- Memuat library untuk ekspor PDF & Excel -->
    <script src="https://unpkg.com/jspdf@2.5.1/dist/jspdf.umd.min.js"></script>
    <script src="https://unpkg.com/jspdf-autotable@3.5.23/dist/jspdf.plugin.autotable.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        /* Menggunakan font Inter sebagai font utama */
        body {
            font-family: 'Inter', sans-serif;
        }
        /* Style tambahan untuk input number agar tidak menampilkan panah */
        input[type='number']::-webkit-inner-spin-button,
        input[type='number']::-webkit-outer-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        input[type='number'] {
            -moz-appearance: textfield;
        }
    </style>
</head>
<body class="bg-slate-100 flex items-center justify-center min-h-screen p-4">

    <!-- Kontainer Login -->
    <div id="loginContainer" class="w-full max-w-sm bg-white rounded-2xl shadow-xl p-6 md:p-8">
        <div class="text-center mb-6">
            <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/11/TVRI_Logo_2019.svg/2560px-TVRI_Logo_2019.svg.png" alt="Logo TVRI" class="mx-auto h-16 w-auto mb-4" onerror="this.onerror=null;this.src='https://placehold.co/200x60/e2e8f0/475569?text=TVRI';">
            <h1 class="text-2xl font-bold text-slate-800">Login Sistem</h1>
            <p class="text-slate-500 mt-1">Masukkan kredensial Anda.</p>
        </div>
        <form id="loginForm" class="space-y-4">
            <div>
                <label for="username" class="block text-sm font-medium text-slate-600 mb-1">Username</label>
                <input type="text" id="username" name="username" required class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition">
            </div>
            <div>
                <label for="password" class="block text-sm font-medium text-slate-600 mb-1">Password</label>
                <input type="password" id="password" name="password" required class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition">
            </div>
            <p id="loginError" class="text-sm text-red-600 text-center h-4"></p>
            <button type="submit" class="w-full bg-blue-600 text-white font-semibold py-3 px-4 rounded-lg hover:bg-blue-700 focus:outline-none focus:ring-4 focus:ring-blue-300 transition-all duration-300 transform active:scale-95">
                Login
            </button>
        </form>
    </div>

    <!-- Kontainer Kalkulator (Awalnya tersembunyi) -->
    <div id="calculatorContainer" class="w-full max-w-lg bg-white rounded-2xl shadow-xl p-6 md:p-8 hidden">
        <div class="text-center mb-6 relative">
             <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/11/TVRI_Logo_2019.svg/2560px-TVRI_Logo_2019.svg.png" alt="Logo TVRI" class="mx-auto h-16 w-auto mb-4" onerror="this.onerror=null;this.src='https://placehold.co/200x60/e2e8f0/475569?text=TVRI';">
            <h1 class="text-2xl font-bold text-slate-800">Kalkulator Konsumsi Solar</h1>
            <p id="gensetInfoText" class="text-slate-500 mt-1">Genset</p>
            <button id="logoutButton" class="absolute top-0 right-0 bg-red-500 text-white text-xs font-bold py-1 px-3 rounded-full hover:bg-red-600 transition">Logout</button>
        </div>
        <form id="calculationForm" class="space-y-4">
            <div class="space-y-4 p-4 bg-slate-50 rounded-lg border border-slate-200">
                <h3 class="text-md font-semibold text-slate-700 -mb-2">Spesifikasi Genset</h3>
                <div>
                    <label for="lokasi" class="block text-sm font-medium text-slate-600 mb-1">Lokasi</label>
                    <select id="lokasi" name="lokasi" required class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition bg-slate-100 cursor-not-allowed appearance-none" disabled>
                        <option>Bukit Greser</option>
                        <option>Masohi</option>
                        <option>Tual</option>
                        <option>Saumlaki</option>
                        <option>Ternate</option>
                        <option>Sofifi</option>
                        <option>Soasiu</option>
                    </select>
                </div>
                <div id="gensetSpecs" class="text-sm text-slate-600 space-y-1 pt-2 border-t border-slate-200 mt-4">
                    <p><strong>Daya:</strong> <span id="specKva" class="font-medium text-slate-800"></span></p>
                    <p><strong>Kapasitas Tangki:</strong> <span id="specCapacity" class="font-medium text-slate-800"></span></p>
                    <p><strong>Konsumsi:</strong> <span id="specConsumption" class="font-medium text-slate-800"></span></p>
                </div>
            </div>
            <div>
                <label for="startDate" class="block text-sm font-medium text-slate-600 mb-1">Tanggal & Waktu Genset Menyala</label>
                <input type="datetime-local" id="startDate" name="startDate" required readonly class="w-full p-2.5 border border-slate-300 rounded-lg bg-slate-100 cursor-not-allowed">
            </div>
            <div>
                 <label for="durationHours" class="block text-sm font-medium text-slate-600 mb-1">Durasi Genset Menyala</label>
                <div class="flex items-center space-x-3">
                    <div class="w-full">
                        <input type="number" id="durationHours" name="durationHours" placeholder="Jam" min="0" class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition text-center" />
                    </div>
                    <span class="font-bold text-slate-500">:</span>
                    <div class="w-full">
                        <input type="number" id="durationMinutes" name="durationMinutes" placeholder="Menit" min="0" max="59" class="w-full p-2.5 border border-slate-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition text-center" />
                    </div>
                </div>
            </div>
            <div>
                <label for="photoUpload" class="block text-sm font-medium text-slate-600 mb-1">Upload Foto Bukti</label>
                <input type="file" id="photoUpload" name="photoUpload" accept="image/*" class="w-full text-sm text-slate-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100 transition">
                <img id="imagePreview" src="" alt="Pratinjau Gambar" class="mt-2 rounded-lg max-h-40 hidden w-full object-cover"/>
            </div>
            <button type="submit" id="submitButton" class="w-full bg-blue-600 text-white font-semibold py-3 px-4 rounded-lg hover:bg-blue-700 focus:outline-none focus:ring-4 focus:ring-blue-300 transition-all duration-300 transform active:scale-95">
                Hitung & Kirim Data
            </button>
        </form>
        <div id="result" class="mt-6 border-t pt-6 hidden">
             <h2 class="text-lg font-semibold text-slate-800 mb-4">Hasil Perhitungan</h2>
             <div class="space-y-3">
                <div class="flex justify-between items-center p-3 bg-slate-50 rounded-lg">
                    <span class="text-slate-600">Total Konsumsi</span>
                    <span id="totalConsumption" class="font-bold text-slate-800 text-lg">0 Liter</span>
                </div>
                <div class="flex justify-between items-center p-3 bg-slate-50 rounded-lg">
                    <span class="text-slate-600">Konsumsi per Menit</span>
                    <span id="consumptionPerMinute" class="font-bold text-emerald-600 text-lg">0 Liter</span>
                </div>
             </div>
             <div class="mt-5">
                <div class="flex justify-between items-center mb-1">
                     <span class="text-sm font-medium text-slate-600">Sisa Solar di Tangki</span>
                     <span id="remainingFuelText" class="text-sm font-bold text-slate-800">200 / 200 Liter</span>
                </div>
                <div class="w-full bg-gray-200 rounded-full h-4 overflow-hidden">
                    <div id="fuelIndicator" class="h-full rounded-full transition-all duration-500" style="width: 100%; background-color: #22c55e;"></div>
                </div>
             </div>
        </div>
        <div id="status" class="mt-4 text-center text-sm"></div>
    </div>

    <!-- Kontainer Monitoring (Awalnya tersembunyi) -->
    <div id="monitoringContainer" class="w-full max-w-4xl bg-white rounded-2xl shadow-xl p-6 md:p-8 hidden">
        <div class="text-center mb-6 relative">
            <h1 class="text-2xl font-bold text-slate-800">Dashboard Monitoring Genset</h1>
            <p class="text-slate-500 mt-1">Data real-time dari semua lokasi.</p>
            <button id="monitoringLogoutButton" class="absolute top-0 right-0 bg-red-500 text-white text-xs font-bold py-1 px-3 rounded-full hover:bg-red-600 transition">Logout</button>
        </div>

        <div class="flex justify-end gap-3 mb-4">
             <button id="downloadPdf" class="bg-red-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-700 focus:outline-none focus:ring-4 focus:ring-red-300 transition text-sm">
                Download PDF
            </button>
            <button id="downloadExcel" class="bg-green-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-700 focus:outline-none focus:ring-4 focus:ring-green-300 transition text-sm">
                Download Excel
            </button>
        </div>

        <div class="overflow-x-auto">
            <table id="monitoringTable" class="w-full text-sm text-left text-gray-500">
                <thead class="text-xs text-gray-700 uppercase bg-gray-100">
                    <tr>
                        <th scope="col" class="px-6 py-3">Lokasi</th>
                        <th scope="col" class="px-6 py-3">Waktu Laporan</th>
                        <th scope="col" class="px-6 py-3">Durasi Nyala</th>
                        <th scope="col" class="px-6 py-3">Konsumsi (L)</th>
                        <th scope="col" class="px-6 py-3">Sisa Solar (L)</th>
                        <th scope="col" class="px-6 py-3">Foto</th>
                    </tr>
                </thead>
                <tbody id="monitoringTableBody">
                    <!-- Data dari Google Sheet akan ditampilkan di sini -->
                    <!-- Contoh Data Statis -->
                    <tr class="bg-white border-b">
                        <td class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap">Bukit Greser</td>
                        <td class="px-6 py-4">2023-10-27 10:30</td>
                        <td class="px-6 py-4">5 jam 15 mnt</td>
                        <td class="px-6 py-4">63.00</td>
                        <td class="px-6 py-4">137.00</td>
                        <td class="px-6 py-4"><button class="bg-sky-500 text-white px-3 py-1 rounded-md text-xs hover:bg-sky-600 view-photo-btn">Lihat</button></td>
                    </tr>
                    <tr class="bg-gray-50 border-b">
                        <td class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap">Ternate</td>
                        <td class="px-6 py-4">2023-10-27 09:45</td>
                        <td class="px-6 py-4">8 jam 0 mnt</td>
                        <td class="px-6 py-4">61.60</td>
                        <td class="px-6 py-4">38.40</td>
                        <td class="px-6 py-4"><button class="bg-sky-500 text-white px-3 py-1 rounded-md text-xs hover:bg-sky-600 view-photo-btn">Lihat</button></td>
                    </tr>
                     <tr class="bg-white border-b">
                        <td class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap">Masohi</td>
                        <td class="px-6 py-4">2023-10-26 22:10</td>
                        <td class="px-6 py-4">12 jam 30 mnt</td>
                        <td class="px-6 py-4">100.00</td>
                        <td class="px-6 py-4">0.00</td>
                        <td class="px-6 py-4"><button class="bg-sky-500 text-white px-3 py-1 rounded-md text-xs hover:bg-sky-600 view-photo-btn">Lihat</button></td>
                    </tr>
                </tbody>
            </table>
            <p class="text-xs text-center text-slate-400 mt-4">Data di atas adalah contoh. Di aplikasi nyata, data ini akan diambil langsung dari Google Sheets.</p>
        </div>
    </div>
    
    <!-- Modal Foto -->
    <div id="photoModal" class="fixed inset-0 bg-black bg-opacity-60 z-50 flex items-center justify-center p-4 hidden transition-opacity duration-300">
        <div class="bg-white p-4 rounded-lg max-w-lg w-full transform transition-transform duration-300 scale-95">
            <div class="flex justify-between items-center mb-3">
                <h3 class="text-lg font-bold text-slate-800">Foto Laporan</h3>
                <button id="closeModal" class="text-gray-500 hover:text-gray-800 text-3xl leading-none">&times;</button>
            </div>
            <img id="modalImage" src="https://placehold.co/600x400/e2e8f0/475569?text=Contoh+Foto+Genset" alt="Foto Laporan" class="w-full h-auto rounded-md object-contain max-h-[70vh]">
        </div>
    </div>

    <script>
        // --- GANTI DENGAN URL WEB APP ANDA DARI GOOGLE APPS SCRIPT ---
        const scriptURL = 'https://script.google.com/macros/s/AKfycbysmBuvwuOQ7atGDdFbX7mqcV-qfLMyN7kIOAUVX66U--whcJyiLzCrBhZKX4xaIH5P/exec';

        // --- DATABASE KREDENSIAL ---
        const credentials = {
            "Bukit Greser": "Bukit Greser",
            "Masohi": "Masohi",
            "Tual": "Tual",
            "Saumlaki": "Saumlaki",
            "Ternate": "Ternate",
            "Sofifi": "Sofifi",
            "Soasiu": "Soasiu",
            "katim": "tvrimaluku" // Akun Pengawas
        };

        // --- DATABASE SPESIFIKASI GENSET ---
        const gensetData = {
            "Bukit Greser": { kva: "60/66 kVA", capacity: 200, consumption: 12 },
            "Masohi": { kva: "40/44 kVA", capacity: 100, consumption: 8 },
            "Tual": { kva: "40/44 kVA", capacity: 100, consumption: 8 },
            "Saumlaki": { kva: "20/30 kVA", capacity: 75, consumption: 2.5 },
            "Ternate": { kva: "50/55 kVA", capacity: 100, consumption: 7.7 },
            "Sofifi": { kva: "60/66 kVA", capacity: 200, consumption: 12 },
            "Soasiu": { kva: "20/24 kVA", capacity: 75, consumption: 2.5 }
        };

        // --- ELEMEN DOM ---
        const loginContainer = document.getElementById('loginContainer');
        const calculatorContainer = document.getElementById('calculatorContainer');
        const monitoringContainer = document.getElementById('monitoringContainer');
        const loginForm = document.getElementById('loginForm');
        const loginError = document.getElementById('loginError');
        const logoutButton = document.getElementById('logoutButton');
        const monitoringLogoutButton = document.getElementById('monitoringLogoutButton');
        const usernameInput = document.getElementById('username');
        const passwordInput = document.getElementById('password');

        const form = document.getElementById('calculationForm');
        const submitButton = document.getElementById('submitButton');
        const statusDiv = document.getElementById('status');
        const gensetInfoText = document.getElementById('gensetInfoText');
        const durationHoursInput = document.getElementById('durationHours');
        const durationMinutesInput = document.getElementById('durationMinutes');
        const resultDiv = document.getElementById('result');
        const totalConsumptionEl = document.getElementById('totalConsumption');
        const consumptionPerMinuteEl = document.getElementById('consumptionPerMinute');
        const remainingFuelTextEl = document.getElementById('remainingFuelText');
        const fuelIndicatorEl = document.getElementById('fuelIndicator');
        const lokasiSelect = document.getElementById('lokasi');
        const specKvaEl = document.getElementById('specKva');
        const specCapacityEl = document.getElementById('specCapacity');
        const specConsumptionEl = document.getElementById('specConsumption');
        const downloadPdfButton = document.getElementById('downloadPdf');
        const downloadExcelButton = document.getElementById('downloadExcel');
        const photoUpload = document.getElementById('photoUpload');
        const imagePreview = document.getElementById('imagePreview');
        
        // Elemen Modal Foto
        const photoModal = document.getElementById('photoModal');
        const closeModal = document.getElementById('closeModal');
        const modalImage = document.getElementById('modalImage');
        const monitoringTableBody = document.getElementById('monitoringTableBody');


        function updateSpecsDisplay() {
            const selectedLocation = lokasiSelect.value;
            const specs = gensetData[selectedLocation];
            
            if (specs) {
                specKvaEl.textContent = specs.kva;
                specCapacityEl.textContent = `${specs.capacity} Liter`;
                specConsumptionEl.textContent = `${specs.consumption} Liter / Jam`;
                gensetInfoText.textContent = `Genset ${specs.kva} (${selectedLocation})`;
            }
        }
        
        // --- LOGIC APLIKASI ---

        loginForm.addEventListener('submit', function(e) {
            e.preventDefault();
            const username = usernameInput.value;
            const password = passwordInput.value;

            if (username === 'katim' && password === credentials.katim) {
                loginContainer.classList.add('hidden');
                monitoringContainer.classList.remove('hidden');
                loginError.textContent = '';
                // fetchMonitoringData(); 
            }
            else if (credentials[username] && credentials[username] === password) {
                loginContainer.classList.add('hidden');
                calculatorContainer.classList.remove('hidden');
                lokasiSelect.value = username;
                lokasiSelect.disabled = true;
                updateSpecsDisplay();
                loginError.textContent = '';
            } else {
                loginError.textContent = 'Username atau password salah.';
            }
        });
        
        function handleLogout() {
            calculatorContainer.classList.add('hidden');
            monitoringContainer.classList.add('hidden');
            loginContainer.classList.remove('hidden');
            loginForm.reset();
            form.reset();
            resultDiv.classList.add('hidden');
            statusDiv.textContent = '';
            imagePreview.src = '';
            imagePreview.classList.add('hidden');
            setInitialDateTime();
        }

        logoutButton.addEventListener('click', handleLogout);
        monitoringLogoutButton.addEventListener('click', handleLogout);

        form.addEventListener('submit', function(e) {
            e.preventDefault(); 
            const selectedLocation = lokasiSelect.value;
            const specs = gensetData[selectedLocation];
            const hours = parseInt(durationHoursInput.value) || 0;
            const minutes = parseInt(durationMinutesInput.value) || 0;

            if (hours === 0 && minutes === 0) {
                 statusDiv.textContent = 'Harap isi durasi menyala.';
                 statusDiv.className = 'mt-4 text-center text-sm text-red-600';
                 return;
            }

            const tankCapacity = specs.capacity;
            const hourlyConsumption = specs.consumption;
            const minutelyConsumption = hourlyConsumption / 60;
            const totalMinutes = (hours * 60) + minutes;
            const totalConsumption = totalMinutes * minutelyConsumption;
            const remainingFuel = tankCapacity - totalConsumption;
            const finalRemainingFuel = Math.max(0, remainingFuel);
            const remainingPercentage = (finalRemainingFuel / tankCapacity) * 100;
            
            updateResultUI(totalConsumption, finalRemainingFuel, remainingPercentage, tankCapacity, minutelyConsumption);

            if (scriptURL === 'MASUKKAN_URL_WEB_APP_ANDA_DI_SINI' || scriptURL === '') {
                statusDiv.textContent = 'Kesalahan: URL Web App belum diatur. Silakan hubungi administrator.';
                statusDiv.className = 'mt-4 text-center text-sm text-red-600 font-bold';
                return;
            }

            submitButton.disabled = true;
            submitButton.textContent = 'Mengirim...';
            statusDiv.textContent = 'Data sedang dikirim ke spreadsheet...';
            statusDiv.className = 'mt-4 text-center text-sm text-blue-600';

            const formData = new FormData(form);
            formData.append('totalConsumption', totalConsumption.toFixed(2));
            formData.append('remainingFuel', finalRemainingFuel.toFixed(2));
            formData.append('consumptionPerMinute', minutelyConsumption.toFixed(3));
            formData.append('durationText', `${hours} jam ${minutes} mnt`);
            
            // CATATAN PENTING: Untuk aplikasi nyata, Google Apps Script Anda perlu
            // kode khusus untuk menangani upload file ini, menyimpannya ke Google Drive,
            // dan kemudian menyimpan URL file tersebut di Google Sheet.
            // Proses ini lebih kompleks daripada sekadar mengirim data teks.
            // FormData akan secara otomatis menangani file jika skrip sisi server mendukungnya.
            
            fetch(scriptURL, { method: 'POST', body: formData })
                .then(response => response.json())
                .then(data => {
                    console.log('Success:', data);
                    statusDiv.textContent = 'Data berhasil terkirim ke sheet ' + selectedLocation + '!';
                    statusDiv.className = 'mt-4 text-center text-sm text-green-600';
                    submitButton.disabled = false;
                    submitButton.textContent = 'Hitung & Kirim Data';
                })
                .catch(error => {
                    console.error('Error:', error);
                    statusDiv.textContent = 'Gagal mengirim data. Cek koneksi atau pengaturan skrip.';
                    statusDiv.className = 'mt-4 text-center text-sm text-red-600';
                    submitButton.disabled = false;
                    submitButton.textContent = 'Hitung & Kirim Data';
                });
        });
        
        function updateResultUI(totalConsumption, remainingFuel, percentage, tankCapacity, minutelyConsumption) {
            totalConsumptionEl.textContent = `${totalConsumption.toFixed(2)} Liter`;
            consumptionPerMinuteEl.textContent = `${minutelyConsumption.toFixed(3)} Liter`;
            remainingFuelTextEl.textContent = `${remainingFuel.toFixed(2)} / ${tankCapacity} Liter`;

            fuelIndicatorEl.style.width = `${percentage}%`;
            if (percentage > 50) {
                fuelIndicatorEl.style.backgroundColor = '#22c55e'; // Hijau
            } else if (percentage > 20) {
                fuelIndicatorEl.style.backgroundColor = '#f59e0b'; // Kuning
            } else {
                fuelIndicatorEl.style.backgroundColor = '#ef4444'; // Merah
            }
            resultDiv.classList.remove('hidden');
        }
        
        function setInitialDateTime() {
            const pad = (num) => num.toString().padStart(2, '0');
            const startDateInput = document.getElementById('startDate');
            const now = new Date();
            const year = now.getFullYear();
            const month = pad(now.getMonth() + 1);
            const day = pad(now.getDate());
            const hours = pad(now.getHours());
            const minutes = pad(now.getMinutes());
            const formattedDateTime = `${year}-${month}-${day}T${hours}:${minutes}`;
            startDateInput.value = formattedDateTime;
        }

        // --- Fungsi untuk Download Laporan ---
        function downloadPDF() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            
            doc.text("Laporan Monitoring Konsumsi Solar Genset", 14, 16);
            doc.setFontSize(10);
            doc.text(`Dicetak pada: ${new Date().toLocaleString('id-ID')}`, 14, 22);

            doc.autoTable({
                html: '#monitoringTable',
                startY: 28,
                theme: 'grid',
                headStyles: { fillColor: [22, 160, 133] }
            });

            doc.save('laporan-genset.pdf');
        }

        function downloadExcel() {
            const table = document.getElementById('monitoringTable');
            const wb = XLSX.utils.table_to_book(table, {sheet:"Laporan Genset"});
            XLSX.writeFile(wb, 'laporan-genset.xlsx');
        }
        
        // --- Event Listeners Tambahan ---
        downloadPdfButton.addEventListener('click', downloadPDF);
        downloadExcelButton.addEventListener('click', downloadExcel);

        // Event listener untuk pratinjau gambar
        photoUpload.addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(event) {
                    imagePreview.src = event.target.result;
                    imagePreview.classList.remove('hidden');
                }
                reader.readAsDataURL(file);
            }
        });
        
        // Event listener untuk membuka modal
        monitoringTableBody.addEventListener('click', function(e) {
            if (e.target.classList.contains('view-photo-btn')) {
                // Di aplikasi nyata, Anda akan mendapatkan URL gambar spesifik dari data
                // Untuk contoh ini, kita gunakan placeholder
                modalImage.src = 'https://placehold.co/600x400/e2e8f0/475569?text=Contoh+Foto+Genset';
                photoModal.classList.remove('hidden');
                photoModal.querySelector('div').classList.remove('scale-95');

            }
        });

        // Event listener untuk menutup modal
        function hideModal() {
             photoModal.querySelector('div').classList.add('scale-95');
             photoModal.classList.add('hidden');
        }
        closeModal.addEventListener('click', hideModal);
        photoModal.addEventListener('click', (e) => {
            if (e.target === photoModal) {
                 hideModal();
            }
        });


        document.addEventListener('DOMContentLoaded', () => {
            setInitialDateTime();
        });
    </script>
</body>
</html>

