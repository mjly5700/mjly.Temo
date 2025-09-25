<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تطبيق إدارة بيانات الطلاب</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Tajawal', sans-serif;
            direction: rtl;
            background-color: #f3f4f6;
        }
        .container {
            max-width: 900px;
        }
        .card {
            background-color: #fff;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            border-radius: 12px;
            padding: 2rem;
        }
        .btn-primary {
            background-color: #4f46e5;
            color: #fff;
            border-radius: 8px;
            transition: background-color 0.2s;
        }
        .btn-primary:hover {
            background-color: #4338ca;
        }
        .form-input, .form-select {
            border: 1px solid #d1d5da;
            border-radius: 8px;
            padding: 0.75rem;
            width: 100%;
        }
        .table-header {
            background-color: #e2e8f0;
        }
        .table-row {
            border-bottom: 1px solid #e2e8f0;
        }
        .table-row:nth-child(even) {
            background-color: #f8fafc;
        }
        .action-button {
            cursor: pointer;
            transition: transform 0.2s;
        }
        .action-button:hover {
            transform: scale(1.1);
        }
        .modal {
            display: none;
            position: fixed;
            z-index: 100;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.4);
            justify-content: center;
            align-items: center;
        }
        .modal-content {
            background-color: #fefefe;
            padding: 20px;
            border-radius: 12px;
            max-width: 400px;
            width: 90%;
            text-align: right; /* Changed to right for form content */
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }
        .modal-content-lg {
            max-width: 600px; /* Wider for edit form */
        }
        .close-button {
            color: #aaa;
            float: left;
            font-size: 28px;
            font-weight: bold;
        }
        .close-button:hover,
        .close-button:focus {
            color: black;
            text-decoration: none;
            cursor: pointer;
        }
        .dropdown {
            position: relative;
            display: inline-block;
        }
        .dropdown-content {
            display: none;
            position: absolute;
            background-color: #fff;
            min-width: 160px;
            box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
            z-index: 10;
            border-radius: 8px;
            padding: 8px;
            text-align: right;
            left: 0; 
        }
        .dropdown-content button {
            color: black;
            padding: 12px 16px;
            text-decoration: none;
            display: block;
            width: 100%;
            text-align: right;
            border-radius: 6px;
        }
        .dropdown-content button:hover {
            background-color: #f1f1f1;
        }
        .dropdown:hover .dropdown-content {
            display: block;
        }
        /* Spinner styles */
        .spinner {
            border: 4px solid rgba(0, 0, 0, 0.1);
            width: 36px;
            height: 36px;
            border-radius: 50%;
            border-left-color: #4f46e5;
            animation: spin 1s ease infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .loading-overlay { 
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(255, 255, 255, 0.8); 
            z-index: 999;
            display: flex;
            justify-content: center;
            align-items: center;
            display: none; 
        }
    </style>
</head>
<body class="p-6">
    <div id="loadingOverlay" class="loading-overlay">
        <div class="spinner"></div>
    </div>
    
    <div class="container mx-auto">
        <div class="card mb-4">
            <p class="text-sm text-gray-600 text-center">
                معرف المستخدم: <span id="userIdDisplay" class="font-bold text-gray-800 break-words">...</span>
            </p>
        </div>

        <div class="card mb-8">
            <h1 class="text-3xl font-bold text-center text-gray-800 mb-6">نموذج إدخال بيانات الطلاب</h1>
            <p class="text-center text-gray-600 mb-8">
                أدخل البيانات الثابتة لجميع الطلاب أولاً، ثم أضف بيانات الطلاب بشكل منفرد.
            </p>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                    <label for="college" class="block text-sm font-medium text-gray-700 mb-1">الكلية</label>
                    <select id="college" class="form-select" required>
                        <option value="">-- اختر الكلية --</option>
                        <option value="كلية الشريعة والقانون">كلية الشريعة والقانون</option>
                        <option value="كلية التربية">كلية التربية</option>
                        <option value="كلية الآداب والعلوم الإنسانية">كلية الآداب والعلوم الإنسانية</option>
                        <option value="كلية العلوم">كلية العلوم</option>
                        <option value="كلية التجارة والاقتصاد">كلية التجارة والاقتصاد</option>
                        <option value="كلية الطب والعلوم الصحية">كلية الطب والعلوم الصحية</option>
                        <option value="كلية الهندسة">كلية الهندسة</option>
                        <option value="كلية الزراعة والأغذية والبيئة">كلية الزراعة والأغذية والبيئة</option>
                        <option value="كلية الصيدلة">كلية الصيدلة</option>
                        <option value="كلية الإعلام">كلية الإعلام</option>
                        <option value="كلية اللغات">كلية اللغات</option>
                        <option value="كلية طب الأسنان">كلية طب الأسنان</option>
                        <option value="كلية الحاسوب وتكنولوجيا المعلومات">كلية الحاسوب وتكنولوجيا المعلومات</option>
                        <option value="كلية التربية الرياضية">كلية التربية الرياضية</option>
                        <option value="كلية الطب البيطري">كلية الطب البيطري</option>
                        <option value="كلية البترول والموارد الطبيعية">كلية البترول والموارد الطبيعية</option>
                        <option value="كلية العلوم الطبية التطبيقية">كلية العلوم الطبية التطبيقية</option>
                        <option value="مركز الحاسب الآلي">مركز الحاسب الآلي</option>
                    </select>
                </div>
                <div>
                    <label for="level" class="block text-sm font-medium text-gray-700 mb-1">المستوى</label>
                    <select id="level" class="form-select" required>
                        <option value="">-- اختر المستوى --</option>
                        <option value="المستوى الأول">المستوى الأول</option>
                        <option value="المستوى الثاني">المستوى الثاني</option>
                        <option value="المستوى الثالث">المستوى الثالث</option>
                        <option value="المستوى الرابع">المستوى الرابع</option>
                        <option value="المستوى الخامس">المستوى الخامس</option>
                        <option value="المستوى السادس">المستوى السادس</option>
                    </select>
                </div>
                <div>
                    <label for="subject" class="block text-sm font-medium text-gray-700 mb-1">اسم المادة</label>
                    <input type="text" id="subject" class="form-input" placeholder="أدخل اسم المادة" required>
                </div>
                <div>
                    <label for="exam-date" class="block text-sm font-medium text-gray-700 mb-1">تاريخ الاختبار</label>
                    <input type="date" id="exam-date" class="form-input" required>
                </div>
            </div>

            <div class="flex justify-center mt-6">
                <button id="add-students-btn" class="w-full md:w-auto px-6 py-2 btn-primary">إضافة طالب أو أكثر</button>
            </div>
        </div>

        <div id="dynamic-fields-container" class="card mt-8 hidden">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">إدخال بيانات الطلاب</h2>
            <form id="dynamic-form" onsubmit="addStudents(event)">
                </form>
        </div>

        <div id="table-container" class="card mt-8 overflow-x-auto hidden">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">كشف تفصيلي</h2>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6 p-4 bg-gray-50 rounded-lg">
                <div>
                    <label for="search-input" class="block text-sm font-medium text-gray-700 mb-1">بحث بالاسم أو المادة</label>
                    <input type="text" id="search-input" class="form-input" placeholder="اكتب للبحث..." onkeyup="filterTable()">
                </div>
                <div>
                    <label for="filter-college" class="block text-sm font-medium text-gray-700 mb-1">تصفية حسب الكلية</label>
                    <select id="filter-college" class="form-select" onchange="filterTable()">
                        <option value="">جميع الكليات</option>
                    </select>
                </div>
                <div>
                    <label for="filter-level" class="block text-sm font-medium text-gray-700 mb-1">تصفية حسب المستوى</label>
                    <select id="filter-level" class="form-select" onchange="filterTable()">
                        <option value="">جميع المستويات</option>
                        <option value="المستوى الأول">المستوى الأول</option>
                        <option value="المستوى الثاني">المستوى الثاني</option>
                        <option value="المستوى الثالث">المستوى الثالث</option>
                        <option value="المستوى الرابع">المستوى الرابع</option>
                        <option value="المستوى الخامس">المستوى الخامس</option>
                        <option value="المستوى السادس">المستوى السادس</option>
                    </select>
                </div>
            </div>
            <table id="data-table" class="min-w-full divide-y divide-gray-200">
                <thead class="table-header">
                    <tr>
                        <th class="px-6 py-3 text-right text-xs font-bold text-gray-700 uppercase tracking-wider">اسم الطالب</th>
                        <th class="px-6 py-3 text-right text-xs font-bold text-gray-700 uppercase tracking-wider">الكلية</th>
                        <th class="px-6 py-3 text-right text-xs font-bold text-gray-700 uppercase tracking-wider">المستوى</th>
                        <th class="px-6 py-3 text-right text-xs font-bold text-gray-700 uppercase tracking-wider">المادة</th>
                        <th class="px-6 py-3 text-right text-xs font-bold text-gray-700 uppercase tracking-wider">تاريخ الاختبار</th>
                        <th class="px-6 py-3 text-right text-xs font-bold text-gray-700 uppercase tracking-wider">المبلغ المتبقي</th>
                        <th class="px-6 py-3 text-right text-xs font-bold text-gray-700 uppercase tracking-wider action-col">إجراء</th>
                    </tr>
                </thead>
                <tbody id="student-table-body" class="bg-white divide-y divide-gray-200">
                    </tbody>
            </table>

            <div id="export-buttons-container" class="flex justify-center mt-6">
                <div class="dropdown">
                    <button class="w-full md:w-auto px-6 py-2 btn-primary">حفظ الكشف باسم</button>
                    <div id="saveDropdown" class="dropdown-content">
                        <button onclick="saveAs('xlsx')">Excel (.xlsx)</button>
                        <button onclick="saveAs('pdf')">PDF (.pdf)</button>
                        <button onclick="saveAs('docx')">Word (.docx)</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div id="countModal" class="modal">
        <div class="modal-content">
            <span class="close-button" onclick="closeModal('countModal')">&times;</span>
            <h3 class="text-xl font-bold mb-4">أدخل عدد الطلاب</h3>
            <input type="number" id="numStudentsInput" class="form-input mb-4" placeholder="العدد" min="1">
            <button onclick="createStudentFields()" class="px-6 py-2 btn-primary">تأكيد</button>
        </div>
    </div>

    <div id="messageModal" class="modal">
        <div class="modal-content">
            <span class="close-button" onclick="closeModal('messageModal')">&times;</span>
            <h3 id="messageTitle" class="text-xl font-bold mb-4"></h3>
            <p id="messageBody" class="mb-4"></p>
            <button onclick="closeModal('messageModal')" class="px-6 py-2 btn-primary">إغلاق</button>
        </div>
    </div>

    <div id="editModal" class="modal">
        <div class="modal-content modal-content-lg">
            <span class="close-button" onclick="closeModal('editModal')">&times;</span>
            <h3 class="text-xl font-bold mb-6 text-center">تعديل بيانات الطالب</h3>
            <form id="edit-form" onsubmit="updateStudent(event)">
                <input type="hidden" id="edit-doc-id">
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label for="edit-student-name" class="block text-sm font-medium text-gray-700 mb-1">اسم الطالب</label>
                        <input type="text" id="edit-student-name" class="form-input" required>
                    </div>
                    <div>
                        <label for="edit-amount-due" class="block text-sm font-medium text-gray-700 mb-1">المبلغ المتبقي</label>
                        <input type="number" id="edit-amount-due" class="form-input" required min="0">
                    </div>
                    <div>
                        <label for="edit-college" class="block text-sm font-medium text-gray-700 mb-1">الكلية</label>
                        <select id="edit-college" class="form-select" required>
                            </select>
                    </div>
                    <div>
                        <label for="edit-level" class="block text-sm font-medium text-gray-700 mb-1">المستوى</label>
                        <select id="edit-level" class="form-select" required>
                            <option value="المستوى الأول">المستوى الأول</option>
                            <option value="المستوى الثاني">المستوى الثاني</option>
                            <option value="المستوى الثالث">المستوى الثالث</option>
                            <option value="المستوى الرابع">المستوى الرابع</option>
                            <option value="المستوى الخامس">المستوى الخامس</option>
                            <option value="المستوى السادس">المستوى السادس</option>
                        </select>
                    </div>
                    <div>
                        <label for="edit-subject" class="block text-sm font-medium text-gray-700 mb-1">اسم المادة</label>
                        <input type="text" id="edit-subject" class="form-input" required>
                    </div>
                    <div>
                        <label for="edit-exam-date" class="block text-sm font-medium text-gray-700 mb-1">تاريخ الاختبار</label>
                        <input type="date" id="edit-exam-date" class="form-input" required>
                    </div>
                </div>
                
                <div class="flex justify-center mt-6">
                    <button type="submit" class="w-full md:w-auto px-6 py-2 btn-primary">حفظ التعديلات</button>
                </div>
            </form>
        </div>
    </div>

    <script type="module" src="https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js"></script>
    <script type="module" src="https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js"></script>
    <script type="module" src="https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/docx/7.1.0/docx.min.js"></script>
    <script type="module" src="https://cdn.sheetjs.com/xlsx-0.20.1/package/xlsx.mjs"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.14/jspdf.plugin.autotable.min.js"></script>


    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        // Import 'updateDoc' for editing
        import { getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc, query, orderBy, updateDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import * as XLSX from "https://cdn.sheetjs.com/xlsx-0.20.1/package/xlsx.mjs";

        // Global variables for Firebase (Placeholder)
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        // IMPORTANT: Replace {} with your actual Firebase config
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {}; 
        
        // Initialize Firebase
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        let userId = null;
        let allStudentsData = []; // Store all fetched students locally for filtering

        // DOM Elements
        const addStudentsBtn = document.getElementById('add-students-btn');
        const countModal = document.getElementById('countModal');
        const studentTableBody = document.getElementById('student-table-body');
        const messageModal = document.getElementById('messageModal');
        const loadingOverlay = document.getElementById('loadingOverlay'); 
        
        // Filter DOM Elements
        const searchInput = document.getElementById('search-input');
        const filterCollege = document.getElementById('filter-college');
        const collegeDropdown = document.getElementById('college'); 

        // New Edit Modal DOM Elements
        const editModal = document.getElementById('editModal');
        const editDocId = document.getElementById('edit-doc-id');
        const editStudentName = document.getElementById('edit-student-name');
        const editAmountDue = document.getElementById('edit-amount-due');
        const editCollege = document.getElementById('edit-college');
        const editLevel = document.getElementById('edit-level');
        const editSubject = document.getElementById('edit-subject');
        const editExamDate = document.getElementById('edit-exam-date');
        
        // Firestore collection reference
        const studentsCollection = () => collection(db, `artifacts/${appId}/users/${userId}/students`);

        // --- Authentication Logic ---
        onAuthStateChanged(auth, async (user) => {
            if (user) {
                userId = user.uid;
                document.getElementById('userIdDisplay').textContent = userId;
                populateCollegeFilter(); 
                populateEditCollegeDropdown(); // NEW: Populate college options in edit modal
                listenForStudents();
            } else {
                try {
                    await signInAnonymously(auth);
                } catch (error) {
                    console.error("Error during anonymous sign-in:", error);
                    showMessage('خطأ في الاتصال', 'تعذر تسجيل الدخول المجهول. يرجى التحقق من إعدادات Firebase.');
                }
            }
        });

        // Function to populate the College filter dropdown
        function populateCollegeFilter() {
            const options = Array.from(collegeDropdown.options);
            filterCollege.innerHTML = '<option value="">جميع الكليات</option>';
            options.forEach(option => {
                if (option.value !== "") {
                    const newOption = document.createElement('option');
                    newOption.value = option.value;
                    newOption.textContent = option.textContent;
                    filterCollege.appendChild(newOption);
                }
            });
        }

        // NEW: Function to populate the College dropdown in the edit modal
        function populateEditCollegeDropdown() {
             const options = Array.from(collegeDropdown.options);
            editCollege.innerHTML = ''; // Clear edit dropdown first
            options.forEach(option => {
                if (option.value !== "") {
                    const newOption = document.createElement('option');
                    newOption.value = option.value;
                    newOption.textContent = option.textContent;
                    editCollege.appendChild(newOption);
                }
            });
        }
        
        // --- Modal/Message Functions (Unchanged) ---
        function showMessage(title, body) {
            document.getElementById('messageTitle').textContent = title;
            document.getElementById('messageBody').textContent = body;
            messageModal.style.display = 'flex';
        }

        window.closeModal = function(modalId) {
            const modal = document.getElementById(modalId);
            modal.style.display = 'none';
        }
        
        // ... (addStudentsBtn listener and createStudentFields are unchanged)
        addStudentsBtn.addEventListener('click', function() {
            const college = document.getElementById('college').value;
            const level = document.getElementById('level').value;
            const subject = document.getElementById('subject').value;
            const examDate = document.getElementById('exam-date').value;

            if (!college || !level || !subject || !examDate) {
                showMessage('بيانات ثابتة مفقودة', 'الرجاء ملء جميع الحقول الثابتة أولاً (الكلية، المستوى، المادة، التاريخ).');
                return;
            }
            countModal.style.display = 'flex';
            document.getElementById('numStudentsInput').focus();
        });

        window.createStudentFields = function() {
            const numStudentsInput = document.getElementById('numStudentsInput');
            const numStudents = parseInt(numStudentsInput.value, 10);
            const dynamicForm = document.getElementById('dynamic-form');
            const dynamicFieldsContainer = document.getElementById('dynamic-fields-container');

            if (numStudents > 0) {
                dynamicForm.innerHTML = '';
                for (let i = 0; i < numStudents; i++) {
                    const studentFields = document.createElement('div');
                    studentFields.classList.add('grid', 'grid-cols-1', 'md:grid-cols-2', 'gap-4', 'mb-4', 'md:col-span-2', 'p-4', 'bg-gray-50', 'rounded-lg');
                    studentFields.innerHTML = `
                        <div>
                            <label for="student-name-${i}" class="block text-sm font-medium text-gray-700 mb-1">اسم الطالب ${i + 1}</label>
                            <input type="text" id="student-name-${i}" class="form-input" placeholder="أدخل اسم الطالب" required>
                        </div>
                        <div>
                            <label for="amount-due-${i}" class="block text-sm font-medium text-gray-700 mb-1">المبلغ المتبقي</label>
                            <input type="number" id="amount-due-${i}" class="form-input" placeholder="أدخل المبلغ المتبقي" required min="0" value="0">
                        </div>
                    `;
                    dynamicForm.appendChild(studentFields);
                }
                const submitBtn = document.createElement('div');
                submitBtn.classList.add('md:col-span-2', 'flex', 'justify-center', 'mt-4');
                submitBtn.innerHTML = '<button type="submit" class="w-full md:w-auto px-6 py-2 btn-primary">إضافة إلى الكشف</button>';
                dynamicForm.appendChild(submitBtn);
                dynamicFieldsContainer.classList.remove('hidden');
                closeModal('countModal');
                dynamicFieldsContainer.scrollIntoView({ behavior: 'smooth' });
            } else {
                showMessage('خطأ في الإدخال', 'الرجاء إدخال عدد صحيح موجب.');
            }
        }

        window.addStudents = async function(e) {
            e.preventDefault();

            const college = document.getElementById('college').value;
            const level = document.getElementById('level').value;
            const subject = document.getElementById('subject').value;
            const examDate = document.getElementById('exam-date').value;

            loadingOverlay.style.display = 'flex'; 
            const dynamicForm = document.getElementById('dynamic-form');
            const dynamicFieldsContainer = document.getElementById('dynamic-fields-container');
            const numStudents = dynamicForm.querySelectorAll('input[id^="student-name"]').length;
            const studentsToAdd = [];

            for (let i = 0; i < numStudents; i++) {
                const studentNameInput = document.getElementById(`student-name-${i}`);
                const amountDueInput = document.getElementById(`amount-due-${i}`);

                if (studentNameInput && amountDueInput) {
                    const studentName = studentNameInput.value.trim();
                    const amountDue = parseFloat(amountDueInput.value);

                    if (studentName && !isNaN(amountDue)) {
                        studentsToAdd.push({
                            studentName,
                            college,
                            level,
                            subject,
                            examDate,
                            amountDue: amountDue,
                            timestamp: new Date().toISOString()
                        });
                    }
                }
            }
            
            if (studentsToAdd.length === 0) {
                loadingOverlay.style.display = 'none';
                showMessage('لا توجد بيانات', 'الرجاء إدخال أسماء ومبالغ الطلاب المراد إضافتهم.');
                return;
            }

            try {
                for (const student of studentsToAdd) {
                    await addDoc(studentsCollection(), student);
                }
                showMessage('نجاح', `تم إضافة ${studentsToAdd.length} طالب إلى الكشف بنجاح.`);
                dynamicForm.innerHTML = ''; 
                dynamicFieldsContainer.classList.add('hidden');
            } catch (e) {
                showMessage('خطأ في الحفظ', `حدث خطأ أثناء إضافة البيانات: ${e.message}`);
            } finally {
                loadingOverlay.style.display = 'none'; 
            }
        }

        // --- Data Display and Filtering Functions (UPDATED) ---

        // Function to render the table rows based on filtered data
        function renderTable(data) {
            studentTableBody.innerHTML = '';
            
            data.forEach((item) => {
                const newRow = document.createElement('tr');
                newRow.classList.add('table-row');
                newRow.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${item.studentName}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.college}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.level}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.subject}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.examDate}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.amountDue.toFixed(2)}</td> 
                    <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium action-col-data">
                        <button onclick="openEditModal('${item.id}')" class="text-blue-600 hover:text-blue-900 action-button ml-2">تعديل</button>
                        <button onclick="deleteRow('${item.id}')" class="text-red-600 hover:text-red-900 action-button">حذف</button>
                    </td>
                `;
                studentTableBody.appendChild(newRow);
            });

            const tableContainer = document.getElementById('table-container');
            if (data.length > 0) {
                tableContainer.classList.remove('hidden');
            } else if (allStudentsData.length === 0) {
                 tableContainer.classList.add('hidden');
            }
        }

        // Function to filter the local data based on inputs
        window.filterTable = function() {
            const searchTerm = searchInput.value.toLowerCase().trim();
            const collegeFilter = filterCollege.value;
            const levelFilter = document.getElementById('filter-level').value;

            const filteredData = allStudentsData.filter(student => {
                const matchesSearch = searchTerm === '' ||
                                      student.studentName.toLowerCase().includes(searchTerm) ||
                                      student.subject.toLowerCase().includes(searchTerm);
                
                const matchesCollege = collegeFilter === '' || student.college === collegeFilter;
                const matchesLevel = levelFilter === '' || student.level === levelFilter;

                return matchesSearch && matchesCollege && matchesLevel;
            });

            renderTable(filteredData);
        }

        // Listens to Firestore changes, saves data locally, and triggers display
        function listenForStudents() {
            const studentQuery = query(studentsCollection(), orderBy('timestamp', 'desc'));

            onSnapshot(studentQuery, (querySnapshot) => {
                allStudentsData = [];
                querySnapshot.forEach((doc) => {
                    const student = doc.data();
                    student.id = doc.id; 
                    allStudentsData.push(student);
                });

                filterTable(); 

                if (allStudentsData.length === 0) {
                    document.getElementById('table-container').classList.add('hidden');
                }

            }, (error) => {
                console.error("Error listening to Firestore:", error);
                showMessage('خطأ في استرداد البيانات', 'حدث خطأ أثناء جلب بيانات الطلاب من قاعدة البيانات.');
            });
        }

        // Deletes a document from Firestore (Unchanged)
        window.deleteRow = async function(docId) {
            if (!confirm('هل أنت متأكد من حذف هذا الطالب؟')) return;
            loadingOverlay.style.display = 'flex';
            try {
                await deleteDoc(doc(studentsCollection(), docId));
                showMessage('تم الحذف', 'تم حذف الطالب بنجاح.');
            } catch (e) {
                showMessage('خطأ في الحذف', `حدث خطأ أثناء حذف الطالب: ${e.message}`);
            } finally {
                loadingOverlay.style.display = 'none';
            }
        }
        
        // --- NEW: Edit Functions ---

        window.openEditModal = function(docId) {
            const student = allStudentsData.find(s => s.id === docId);
            if (student) {
                // Set the document ID (critical for update)
                editDocId.value = docId; 
                
                // Populate the form fields
                editStudentName.value = student.studentName;
                editAmountDue.value = student.amountDue.toString();
                editCollege.value = student.college;
                editLevel.value = student.level;
                editSubject.value = student.subject;
                editExamDate.value = student.examDate;

                // Show the modal
                editModal.style.display = 'flex';
            } else {
                showMessage('خطأ', 'لم يتم العثور على بيانات الطالب.');
            }
        }

        window.updateStudent = async function(e) {
            e.preventDefault();
            loadingOverlay.style.display = 'flex';
            
            const id = editDocId.value;
            
            const updatedData = {
                studentName: editStudentName.value.trim(),
                amountDue: parseFloat(editAmountDue.value),
                college: editCollege.value,
                level: editLevel.value,
                subject: editSubject.value.trim(),
                examDate: editExamDate.value,
                // Note: We don't update the timestamp here to keep the original ordering context
            };
            
            if (!id || !updatedData.studentName || isNaN(updatedData.amountDue) || !updatedData.college || !updatedData.level || !updatedData.subject || !updatedData.examDate) {
                loadingOverlay.style.display = 'none';
                showMessage('خطأ في البيانات', 'الرجاء التأكد من صحة جميع المدخلات.');
                return;
            }

            try {
                await updateDoc(doc(studentsCollection(), id), updatedData);
                closeModal('editModal');
                showMessage('نجاح', 'تم تحديث بيانات الطالب بنجاح.');
            } catch (e) {
                showMessage('خطأ في التحديث', `حدث خطأ أثناء تحديث البيانات: ${e.message}`);
            } finally {
                loadingOverlay.style.display = 'none';
            }
        }

        // --- Export Functions (Unchanged) ---
        window.saveAs = async function(format) {
             const table = document.querySelector('#data-table');
            if (table.tBodies[0].rows.length === 0) {
                showMessage("لا توجد بيانات", "لا توجد بيانات في الجدول للتصدير.");
                return;
            }

            loadingOverlay.style.display = 'flex'; 
            
            const exportButtonsContainer = document.getElementById('export-buttons-container');
            const actionColumnHeader = document.querySelector('.action-col');
            
            // Temporary hide action columns for accurate table capture
            exportButtonsContainer.style.display = 'none';
            actionColumnHeader.style.display = 'none';
            document.querySelectorAll('.action-col-data').forEach(el => el.style.display = 'none');
            
            await new Promise(resolve => setTimeout(resolve, 50));

            try {
                // ... (XLSX, PDF, DOCX logic remains the same)
                 if (format === 'xlsx') {
                    const ws = XLSX.utils.table_to_sheet(table);
                    const wb = XLSX.utils.book_new();
                    XLSX.utils.book_append_sheet(wb, ws, "Students");
                    XLSX.writeFile(wb, "كشف_بيانات_الطلاب.xlsx");
                } else if (format === 'pdf') {
                    const { jsPDF } = window.jspdf;
                    const doc = new jsPDF();
                    const headers = ["اسم الطالب", "الكلية", "المستوى", "المادة", "تاريخ الاختبار", "المبلغ المتبقي"];
                    const data = Array.from(document.querySelectorAll('#student-table-body tr')).map(row => {
                        return Array.from(row.querySelectorAll('td')).slice(0, 6).map(cell => cell.textContent);
                    });
                    
                    doc.text("كشف تفصيلي ببيانات الطلاب", 105, 20, null, null, "center");

                    doc.autoTable({
                        startY: 30,
                        head: [headers.reverse()], 
                        body: data.map(row => row.reverse()), 
                        theme: 'grid',
                        styles: {
                            halign: 'right', 
                            cellPadding: 2,
                            fontSize: 10,
                            direction: 'rtl' 
                        },
                        headStyles: {
                            fontStyle: 'bold',
                            halign: 'right',
                            fillColor: [226, 232, 240]
                        },
                    });
                    doc.save('كشف_بيانات_الطلاب.pdf');
                } else if (format === 'docx') {
                    const tableRows = Array.from(table.tBodies[0].rows);
                    const tableData = tableRows.map(row => 
                        Array.from(row.cells).slice(0, 6).map(cell => new docx.Paragraph({
                            children: [new docx.TextRun({ text: cell.textContent, rightToLeft: true, font: { name: "Arial" } })],
                        }))
                    );
                    const tableHeaders = Array.from(table.tHead.rows[0].cells).slice(0, 6).map(header => new docx.Paragraph({
                        children: [new docx.TextRun({ text: header.textContent, bold: true, rightToLeft: true, font: { name: "Arial" } })],
                    }));

                    const doc = new docx.Document({
                        sections: [{
                            properties: { page: { size: { orientation: docx.PageOrientation.PORTRAIT, width: 11906, height: 16838 } } },
                            children: [
                                new docx.Paragraph({
                                    children: [new docx.TextRun({ text: "كشف تفصيلي ببيانات الطلاب", bold: true, size: 36, rightToLeft: true, font: { name: "Arial" } })],
                                    alignment: docx.AlignmentType.CENTER,
                                }),
                                new docx.Table({
                                    rows: [
                                        new docx.TableRow({ children: tableHeaders.reverse() }),
                                        ...tableData.map(row => new docx.TableRow({ children: row.reverse() }))
                                    ],
                                    margins: { top: 200, bottom: 200, left: 200, right: 200 },
                                }),
                            ],
                        }],
                    });

                    await docx.Packer.toBlob(doc).then(blob => {
                        saveAs(blob, "كشف_بيانات_الطلاب.docx");
                    });
                }
                
                showMessage('تم التصدير', `تم حفظ الكشف بنجاح بصيغة ${format.toUpperCase()}.`);

            } catch (e) {
                console.error("Export Error:", e);
                showMessage('خطأ في التصدير', `حدث خطأ: ${e.message}. تأكد من تحديث المتصفح.`);
            } finally {
                // Restore hidden elements and hide spinner
                exportButtonsContainer.style.display = 'flex';
                actionColumnHeader.style.display = 'table-cell';
                document.querySelectorAll('.action-col-data').forEach(el => el.style.display = 'table-cell');
                loadingOverlay.style.display = 'none';
            }
        }
    </script>
</body>
</html>
