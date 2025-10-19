<!doctype html>
<html lang="id">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <title>HadirQu — Aplikasi Absensi</title>
    <style>
        :root{
            --bg:#f6f8fb;--card:#fff;--accent:#2563eb;--muted:#6b7280;--danger:#ef4444;
            --radius:10px;
            font-family:Inter,ui-sans-serif,system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial;
        }
        *{box-sizing:border-box}
        body{margin:0;background:linear-gradient(180deg,#eef2ff 0,#f6f8fb 60%);color:#0f172a;min-height:100vh}
        .container{max-width:980px;margin:28px auto;padding:20px}
        header{display:flex;align-items:center;gap:16px;margin-bottom:18px}
        .brand{display:flex;gap:12px;align-items:center}
        .logo{width:48px;height:48px;border-radius:10px;background:linear-gradient(135deg,var(--accent),#7c3aed);display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;font-size:18px}
        h1{font-size:20px;margin:0}
        p.lead{margin:0;color:var(--muted);font-size:14px}
        .card{background:var(--card);border-radius:var(--radius);padding:18px;box-shadow:0 6px 18px rgba(15,23,42,0.06);margin-bottom:14px}
        .grid{display:grid;grid-template-columns:1fr 320px;gap:16px}
        @media (max-width:880px){.grid{grid-template-columns:1fr}}
        .form-row{display:flex;gap:8px;align-items:center}
        label{font-size:13px;color:var(--muted);margin-bottom:6px;display:block}
        input[type="text"],input[type="password"],select{width:100%;padding:10px;border-radius:8px;border:1px solid #e6e9ef;background:#fff}
        button{background:var(--accent);color:#fff;padding:10px 12px;border-radius:8px;border:none;cursor:pointer}
        button.ghost{background:transparent;color:var(--accent);border:1px solid rgba(37,99,235,0.12)}
        button.danger{background:var(--danger)}
        .small{font-size:13px;color:var(--muted)}
        .actions{display:flex;gap:8px;flex-wrap:wrap}
        .attendance-controls{display:flex;gap:8px;flex-wrap:wrap;margin-top:8px}
        table{width:100%;border-collapse:collapse;margin-top:8px}
        th,td{padding:8px;text-align:left;border-bottom:1px solid #f1f5f9;font-size:14px}
        .muted{color:var(--muted);font-size:13px}
        .notice{padding:10px;border-radius:8px;background:#fffbe6;border:1px solid #ffe8a1;color:#7c5a00}
        footer{margin-top:18px;text-align:center;color:var(--muted);font-size:13px}
        .kbd{background:#0f172a;color:#fff;padding:6px 8px;border-radius:6px;font-weight:600;font-size:12px}
    </style>
</head>
<body>
    <div class="container">
        <header>
            <div class="brand">
                <div class="logo">HQ</div>
                <div>
                    <h1>HadirQu</h1>
                    <p class="lead">Aplikasi absensi sederhana — tanpa server, data lokal (LocalStorage)</p>
                </div>
            </div>
        </header>

        <div class="grid">
            <div>
                <div id="authCard" class="card">
                    <div id="authForms">
                        <div id="loginForm">
                            <label>Masuk</label>
                            <div class="form-row"><input id="loginUser" type="text" placeholder="Username" /></div>
                            <div class="form-row"><input id="loginPass" type="password" placeholder="Password" /></div>
                            <div class="actions" style="margin-top:10px">
                                <button onclick="login()">Masuk</button>
                                <button class="ghost" onclick="showRegister()">Daftar</button>
                            </div>
                            <p class="small" style="margin-top:10px">Belum punya akun? Gunakan "Daftar". Data disimpan hanya di browser Anda.</p>
                        </div>

                        <div id="registerForm" style="display:none">
                            <label>Daftar Akun Baru</label>
                            <div class="form-row"><input id="regUser" type="text" placeholder="Username (unik)" /></div>
                            <div class="form-row"><input id="regName" type="text" placeholder="Nama lengkap" /></div>
                            <div class="form-row"><input id="regPass" type="password" placeholder="Password" /></div>
                            <div class="actions" style="margin-top:10px">
                                <button onclick="register()">Buat Akun</button>
                                <button class="ghost" onclick="showLogin()">Kembali</button>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="card" id="helpCard">
                    <label>Petunjuk Singkat</label>
                    <ul class="small" style="padding-left:18px;margin:8px 0 0 0">
                        <li>Daftar akun lalu masuk</li>
                        <li>Gunakan "Absen Masuk" ketika datang, "Absen Pulang" saat selesai</li>
                        <li>Riwayat tersimpan di browser. Ekspor CSV bila perlu</li>
                    </ul>
                </div>
            </div>

            <aside>
                <div id="panelLoggedOut" class="card" style="display:block">
                    <label>Status</label>
                    <p class="muted">Anda belum masuk. Silakan masuk atau daftar untuk mulai absen.</p>
                </div>

                <div id="panelUser" class="card" style="display:none">
                    <div style="display:flex;justify-content:space-between;align-items:center">
                        <div>
                            <div class="small">Anda masuk sebagai</div>
                            <div id="userName" style="font-weight:700"></div>
                            <div id="userId" class="small muted"></div>
                        </div>
                        <div>
                            <button class="ghost" onclick="logout()">Keluar</button>
                        </div>
                    </div>

                    <div style="margin-top:12px">
                        <div class="small">Lokasi (opsional)</div>
                        <div style="display:flex;gap:8px;margin-top:8px">
                            <button onclick="getLocation()" class="ghost">Ambil Lokasi</button>
                            <button onclick="clearLocation()" class="ghost">Hapus</button>
                        </div>
                        <div id="locationText" class="small muted" style="margin-top:8px">Lokasi belum diambil</div>
                    </div>

                    <div style="margin-top:12px">
                        <div class="small">Absensi</div>
                        <div class="attendance-controls">
                            <button onclick="mark('masuk')">Absen Masuk</button>
                            <button onclick="mark('pulang')" class="ghost">Absen Pulang</button>
                        </div>
                        <div id="lastStatus" class="small muted" style="margin-top:8px">Belum ada aktivitas</div>
                    </div>

                    <div style="margin-top:12px">
                        <div class="small">Ekspor / Hapus</div>
                        <div class="actions" style="margin-top:8px">
                            <button onclick="exportCSV()">Ekspor CSV</button>
                            <button class="danger" onclick="clearAttendance()">Hapus Riwayat</button>
                        </div>
                    </div>
                </div>

                <div id="noticeGPS" class="card notice" style="display:none;margin-top:12px">
                    Aplikasi mencoba mengambil lokasi dari browser. Berikan izin untuk menyimpan koordinat pada saat absen.
                </div>
            </aside>
        </div>

        <div id="historyCard" class="card" style="margin-top:14px;display:none">
            <label>Riwayat Absensi</label>
            <div id="historyInfo" class="small muted" style="margin-top:6px"></div>
            <table id="historyTable" aria-live="polite">
                <thead>
                    <tr><th>Tanggal</th><th>Jam Masuk</th><th>Jam Pulang</th><th>Lokasi</th></tr>
                </thead>
                <tbody></tbody>
            </table>
        </div>

        <footer>
            <div>HadirQu — Local demo. Tidak untuk produksi.</div>
        </footer>
    </div>

    <script>
        // Simple localStorage data model
        const USERS_KEY = 'hadirqu_users';
        const SESSION_KEY = 'hadirqu_session';
        const ATT_KEY_PREFIX = 'hadirqu_att_'; // + username

        function loadUsers(){ return JSON.parse(localStorage.getItem(USERS_KEY) || '{}'); }
        function saveUsers(u){ localStorage.setItem(USERS_KEY, JSON.stringify(u)); }

        function formatDate(d){ return d.toLocaleDateString('id-ID'); }
        function formatTime(d){ return d.toTimeString().split(' ')[0].slice(0,8); }
        function todayKey(){ const d=new Date(); return d.toISOString().slice(0,10); }

        // UI helpers
        function showRegister(){ document.getElementById('loginForm').style.display='none'; document.getElementById('registerForm').style.display='block'; }
        function showLogin(){ document.getElementById('loginForm').style.display='block'; document.getElementById('registerForm').style.display='none'; }

        function register(){
            const username = (document.getElementById('regUser').value || '').trim();
            const name = (document.getElementById('regName').value || '').trim();
            const pass = (document.getElementById('regPass').value || '').trim();
            if(!username || !pass || !name){ alert('Isi semua field'); return; }
            const users = loadUsers();
            if(users[username]){ alert('Username sudah ada'); return; }
            users[username] = { name, pass };
            saveUsers(users);
            alert('Akun dibuat. Silakan masuk.');
            document.getElementById('regUser').value='';document.getElementById('regPass').value='';document.getElementById('regName').value='';
            showLogin();
        }

        function login(){
            const username = (document.getElementById('loginUser').value || '').trim();
            const pass = (document.getElementById('loginPass').value || '').trim();
            const users = loadUsers();
            if(!username || !pass){ alert('Masukkan username dan password'); return; }
            const user = users[username];
            if(!user || user.pass !== pass){ alert('Username atau password salah'); return; }
            localStorage.setItem(SESSION_KEY, username);
            initApp();
        }

        function logout(){
            localStorage.removeItem(SESSION_KEY);
            location.reload();
        }

        function getSession(){ return localStorage.getItem(SESSION_KEY); }

        // Attendance storage per user
        function attendanceKey(user){ return ATT_KEY_PREFIX + user; }
        function loadAttendance(user){
            return JSON.parse(localStorage.getItem(attendanceKey(user)) || '{}'); // date -> {masuk:time, pulang:time, loc}
        }
        function saveAttendance(user, data){
            localStorage.setItem(attendanceKey(user), JSON.stringify(data));
        }

        // Location
        let currentLocation = null;
        function getLocation(){
            if(!navigator.geolocation){ alert('Geolocation tidak tersedia di browser ini'); return; }
            document.getElementById('noticeGPS').style.display='block';
            navigator.geolocation.getCurrentPosition(pos=>{
                currentLocation = {lat:pos.coords.latitude, lng:pos.coords.longitude, acc:pos.coords.accuracy};
                document.getElementById('locationText').textContent = `Lat ${currentLocation.lat.toFixed(5)}, Lng ${currentLocation.lng.toFixed(5)} (akurasi ${Math.round(currentLocation.acc)} m)`;
                document.getElementById('noticeGPS').style.display='none';
            }, err=>{
                document.getElementById('noticeGPS').style.display='none';
                alert('Gagal mengambil lokasi: ' + err.message);
            }, {enableHighAccuracy:true, timeout:8000});
        }
        function clearLocation(){ currentLocation = null; document.getElementById('locationText').textContent='Lokasi belum diambil'; }

        function mark(type){
            const user = getSession();
            if(!user){ alert('Silakan masuk dulu'); return; }
            const att = loadAttendance(user);
            const k = todayKey();
            if(!att[k]) att[k] = { masuk:null, pulang:null, loc:null };
            const now = new Date();
            if(type === 'masuk'){
                if(att[k].masuk){ if(!confirm('Sudah absen masuk hari ini. Timpa?')) return; }
                att[k].masuk = formatTime(now);
                if(currentLocation) att[k].loc = currentLocation;
                saveAttendance(user, att);
                render();
                alert('Absen masuk tersimpan: ' + att[k].masuk);
            } else if(type === 'pulang'){
                if(!att[k].masuk){ if(!confirm('Belum absen masuk hari ini. Tetap absen pulang?')){} }
                att[k].pulang = formatTime(now);
                if(currentLocation) att[k].loc = currentLocation;
                saveAttendance(user, att);
                render();
                alert('Absen pulang tersimpan: ' + att[k].pulang);
            }
        }

        function render(){
            const user = getSession();
            if(!user){
                document.getElementById('panelLoggedOut').style.display='block';
                document.getElementById('panelUser').style.display='none';
                document.getElementById('historyCard').style.display='none';
                return;
            }
            const users = loadUsers();
            const profile = users[user];
            document.getElementById('panelLoggedOut').style.display='none';
            document.getElementById('panelUser').style.display='block';
            document.getElementById('userName').textContent = profile.name || user;
            document.getElementById('userId').textContent = '@' + user;

            const att = loadAttendance(user);
            const rows = Object.keys(att).sort((a,b)=>b.localeCompare(a));
            const tbody = document.querySelector('#historyTable tbody');
            tbody.innerHTML = '';
            rows.forEach(date=>{
                const r = att[date];
                const locText = r.loc ? `Lat:${r.loc.lat.toFixed(5)},Lng:${r.loc.lng.toFixed(5)}` : '-';
                const tr = document.createElement('tr');
                tr.innerHTML = `<td>${date}</td><td>${r.masuk || '-'}</td><td>${r.pulang || '-'}</td><td>${locText}</td>`;
                tbody.appendChild(tr);
            });

            document.getElementById('historyInfo').textContent = rows.length ? `${rows.length} catatan` : 'Belum ada catatan';
            document.getElementById('historyCard').style.display = 'block';
            // last activity
            const today = att[todayKey()];
            const status = today ? `Hari ini — masuk: ${today.masuk || '-'}, pulang: ${today.pulang || '-'}` : 'Belum ada aktivitas hari ini';
            document.getElementById('lastStatus').textContent = status;
            // location display
            if(currentLocation) document.getElementById('locationText').textContent = `Lat ${currentLocation.lat.toFixed(5)}, Lng ${currentLocation.lng.toFixed(5)} (akurasi ${Math.round(currentLocation.acc)} m)`;
        }

        function exportCSV(){
            const user = getSession();
            if(!user){ alert('Silakan masuk'); return; }
            const att = loadAttendance(user);
            const rows = [['tanggal','masuk','pulang','lat','lng','acc']];
            Object.keys(att).sort().forEach(date=>{
                const r = att[date];
                if(r.loc) rows.push([date, r.masuk || '', r.pulang || '', r.loc.lat, r.loc.lng, r.loc.acc]);
                else rows.push([date, r.masuk || '', r.pulang || '', '', '', '']);
            });
            const csv = rows.map(r=>r.map(cell=>`"${String(cell).replace(/"/g,'""')}"`).join(',')).join('\n');
            const blob = new Blob([csv],{type:'text/csv'});
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url; a.download = `hadirqu_${user}.csv`; document.body.appendChild(a); a.click(); a.remove();
            URL.revokeObjectURL(url);
        }

        function clearAttendance(){
            const user = getSession();
            if(!user) return;
            if(!confirm('Hapus semua riwayat absensi untuk akun ini?')) return;
            localStorage.removeItem(attendanceKey(user));
            render();
        }

        // Init
        function initApp(){
            const user = getSession();
            if(user){
                document.getElementById('authCard').style.display='none';
                document.getElementById('panelUser').style.display='block';
            } else {
                document.getElementById('authCard').style.display='block';
            }
            render();
        }

        // prefill demo account for convenience (optional)
        (function bootstrapDemo(){
            const users = loadUsers();
            if(!users['demo']){ users['demo'] = { name:'Demo User', pass:'demo' }; saveUsers(users); }
        })();

        initApp();
        // expose a few functions for buttons
        window.register = register;
        window.login = login;
        window.logout = logout;
        window.showRegister = showRegister;
        window.showLogin = showLogin;
        window.getLocation = getLocation;
        window.clearLocation = clearLocation;
        window.mark = mark;
        window.exportCSV = exportCSV;
        window.clearAttendance = clearAttendance;
    </script>
</body>
</html></div></html>
