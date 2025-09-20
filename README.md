<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>To Do List</title>
<!-- Bootstrap CSS (Toast için) -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet" />
<style>
  body {
    padding: 20px;
    max-width: 500px;
    margin: auto;
  }
  .done {
    text-decoration: line-through;
    color: gray;
  }
  #toast-container {
    position: fixed;
    top: 20px;
    right: 20px;
    z-index: 9999;
  }
</style>
</head>
<body>

<h2>To Do List</h2>

<div class="mb-3">
  <input type="text" id="taskInput" class="form-control" placeholder="Yeni görev ekle..." />
</div>
<button id="addBtn" class="btn btn-primary mb-3">Ekle</button>

<ul id="taskList" class="list-group"></ul>

<!-- Toast Container -->
<div id="toast-container" aria-live="polite" aria-atomic="true"></div>

<!-- Bootstrap JS & Popper (Toast için) -->
<script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.11.7/dist/umd/popper.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.min.js"></script>

<script>
  const taskInput = document.getElementById('taskInput');
  const addBtn = document.getElementById('addBtn');
  const taskList = document.getElementById('taskList');
  const toastContainer = document.getElementById('toast-container');

  // LocalStorage'dan yükle
  let tasks = JSON.parse(localStorage.getItem('tasks')) || [];

  // Görevleri DOM'a yazdır
  function renderTasks() {
    taskList.innerHTML = '';
    tasks.forEach((task, index) => {
      const li = document.createElement('li');
      li.className = 'list-group-item d-flex justify-content-between align-items-center';
      li.innerHTML = `
        <span class="${task.done ? 'done' : ''}">${task.text}</span>
        <div>
          <button class="btn btn-success btn-sm me-2 done-btn">${task.done ? 'Geri Al' : 'Yapıldı'}</button>
          <button class="btn btn-danger btn-sm delete-btn">Sil</button>
        </div>
      `;

      // Yapıldı butonu
      li.querySelector('.done-btn').addEventListener('click', () => {
        tasks[index].done = !tasks[index].done;
        saveAndRender();
        showToast(tasks[index].done ? 'Görev tamamlandı!' : 'Görev işaret kaldırıldı!');
      });

      // Sil butonu
      li.querySelector('.delete-btn').addEventListener('click', () => {
        tasks.splice(index, 1);
        saveAndRender();
        showToast('Görev silindi!');
      });

      taskList.appendChild(li);
    });
  }

  // LocalStorage'a kaydet ve yeniden çiz
  function saveAndRender() {
    localStorage.setItem('tasks', JSON.stringify(tasks));
    renderTasks();
  }

  // Toast gösterimi
  function showToast(message) {
    const toastEl = document.createElement('div');
    toastEl.className = 'toast align-items-center text-bg-primary border-0';
    toastEl.setAttribute('role', 'alert');
    toastEl.setAttribute('aria-live', 'assertive');
    toastEl.setAttribute('aria-atomic', 'true');
    toastEl.innerHTML = `
      <div class="d-flex">
        <div class="toast-body">${message}</div>
        <button type="button" class="btn-close btn-close-white me-2 m-auto" data-bs-dismiss="toast" aria-label="Close"></button>
      </div>
    `;
    toastContainer.appendChild(toastEl);

    const bsToast = new bootstrap.Toast(toastEl, { delay: 2000 });
    bsToast.show();

    // Toast tamamlanınca DOM'dan kaldır
    toastEl.addEventListener('hidden.bs.toast', () => {
      toastEl.remove();
    });
  }

  // Görev ekleme fonksiyonu
  function addTask() {
    const text = taskInput.value.trim();
    if (!text) {
      showToast('Lütfen boş bırakmayın!');
      return;
    }
    tasks.push({ text, done: false });
    taskInput.value = '';
    saveAndRender();
    showToast('Görev eklendi!');
  }

  addBtn.addEventListener('click', addTask);
  taskInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') addTask();
  });

  // Sayfa yüklendiğinde eski görevleri göster
  renderTasks();
</script>

</body>
</html>
