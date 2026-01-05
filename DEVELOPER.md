# 📚 Документация для разработчиков

## 🏗️ Архитектура приложения

Приложение Kanban Board написано на **чистом JavaScript** (ES6+) без использования внешних фреймворков. Это позволяет достичь максимальной производительности и минимальных требований к окружению.

### Основные модули

```
Kanban-doska-rabochaia.html
├── HTML (семантическая разметка)
├── CSS (стили, переменные, адаптивность)
└── JavaScript (логика приложения)
    ├── State Management (управление состоянием)
    ├── DOM Manipulation (работа с DOM)
    ├── Event Handlers (обработчики событий)
    ├── Storage (localStorage API)
    └── AI Integration (интеграция с ИИ)
```

## 🔍 Ключевые функции JavaScript

### 1. Управление состоянием (State Management)

Все задачи хранятся в `localStorage` в JSON формате:

```javascript
// Структура задачи
{
  id: "task_1234567890",
  title: "Название задачи",
  description: "Описание",
  status: "plan", // plan, inprogress, done
  priority: "high", // high, medium, low
  assignee: "Иван Петров",
  avatar: "data:image/png;base64,...",
  labels: ["red", "blue"],
  deadline: "2024-12-31",
  checklist: [
    { id: "1", text: "Подзадача 1", completed: true },
    { id: "2", text: "Подзадача 2", completed: false }
  ],
  createdAt: 1704067200,
  updatedAt: 1704067200
}
```

### 2. Drag & Drop API

Использование встроенного Drag & Drop API для перемещения задач:

```javascript
element.addEventListener('dragstart', (e) => {
  e.dataTransfer.effectAllowed = 'move';
  e.dataTransfer.setData('text/html', e.target);
});

dropZone.addEventListener('drop', (e) => {
  e.preventDefault();
  const data = e.dataTransfer.getData('text/html');
  // Обработка переноса
});
```

### 3. localStorage API

Автоматическое сохранение всех изменений:

```javascript
const saveTasks = () => {
  localStorage.setItem('kanban_tasks', JSON.stringify(tasks));
};

const loadTasks = () => {
  const data = localStorage.getItem('kanban_tasks');
  return data ? JSON.parse(data) : [];
};
```

### 4. Обработка изображений (Canvas API)

Конвертация изображений в base64 для сохранения в localStorage:

```javascript
const imageInput = document.querySelector('input[type="file"]');
imageInput.addEventListener('change', (e) => {
  const reader = new FileReader();
  reader.onload = (event) => {
    const base64 = event.target.result;
    // Сохранение в задачу
  };
  reader.readAsDataURL(e.target.files[0]);
});
```

## 🎨 CSS Архитектура

### CSS Переменные

Все цвета и размеры определены через CSS переменные для легкого переключения темы:

```css
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --success-color: #28a745;
  --danger-color: #dc3545;
  --warning-color: #ffc107;
  
  /* Светлая тема */
  --bg-primary: #ffffff;
  --text-primary: #212529;
  --border-color: #dee2e6;
}

[data-theme="dark"] {
  --bg-primary: #1a1a1a;
  --text-primary: #ffffff;
  --border-color: #444;
}
```

### Адаптивный дизайн

Использование CSS Grid и Flexbox для адаптации:

```css
.kanban-board {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1rem;
}

@media (max-width: 768px) {
  .kanban-board {
    grid-template-columns: 1fr;
  }
}
```

## 🔌 Интеграция с ИИ

### Подключение локального LLM

Приложение поддерживает интеграцию с локальными моделями через API:

```javascript
const suggestWithAI = async (description) => {
  try {
    const response = await fetch('http://localhost:8000/api/suggest', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ description })
    });
    
    const data = await response.json();
    return {
      title: data.title,
      description: data.description,
      priority: data.priority
    };
  } catch (error) {
    console.error('AI Error:', error);
  }
};
```

### Требуемый формат API

Локальный сервер должен реализовать эндпоинт:

```
POST /api/suggest
Content-Type: application/json

{
  "description": "Текст задачи"
}

Response:
{
  "title": "Оптимизированное название",
  "description": "Расширенное описание",
  "priority": "high|medium|low"
}
```

## 📊 Экспорт в CSV

Функция экспорта создает CSV файл с данными задач:

```javascript
const exportToCSV = () => {
  const headers = ['ID', 'Название', 'Описание', 'Статус', 'Приоритет', ...];
  const csv = [headers, ...tasks].map(row => 
    Object.values(row).map(val => `"${val}"`).join(',')
  ).join('\n');
  
  const blob = new Blob([csv], { type: 'text/csv' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'kanban_tasks.csv';
  a.click();
};
```

## 🚀 Расширение функционала

### Добавление новой функции

1. **Создайте функцию обработчик:**
```javascript
function handleNewFeature(taskId) {
  const task = tasks.find(t => t.id === taskId);
  // Логика функции
  saveTasks();
}
```

2. **Добавьте обработчик события:**
```javascript
document.getElementById('newFeatureBtn').addEventListener('click', () => {
  handleNewFeature(selectedTaskId);
});
```

3. **Обновите UI:**
```javascript
function renderTask(task) {
  // Добавьте новый элемент интерфейса
  const newFeatureBtn = document.createElement('button');
  // ...
}
```

### Интеграция с API

Пример подключения REST API:

```javascript
const syncWithServer = async () => {
  const response = await fetch('/api/tasks', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(tasks)
  });
  
  if (response.ok) {
    const syncedTasks = await response.json();
    localStorage.setItem('kanban_tasks', JSON.stringify(syncedTasks));
  }
};
```

## 🔒 Безопасность

### Валидация входных данных

```javascript
function validateTask(task) {
  if (!task.title || task.title.trim() === '') {
    throw new Error('Название задачи не может быть пустым');
  }
  
  if (!['high', 'medium', 'low'].includes(task.priority)) {
    throw new Error('Недопустимый приоритет');
  }
  
  return true;
}
```

### Санитизация HTML

```javascript
function sanitizeHTML(html) {
  const div = document.createElement('div');
  div.textContent = html;
  return div.innerHTML;
}
```

## 📈 Оптимизация производительности

### Мемоизация

```javascript
const memoizedSearch = (() => {
  const cache = new Map();
  return (tasks, query) => {
    if (cache.has(query)) return cache.get(query);
    const result = tasks.filter(t => 
      t.title.toLowerCase().includes(query.toLowerCase())
    );
    cache.set(query, result);
    return result;
  };
})();
```

### Ленивая загрузка

```javascript
const intersectionObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      loadMoreTasks();
    }
  });
});
```

## 🧪 Тестирование

### Пример unit-теста (Vanilla JS)

```javascript
function testAddTask() {
  const initialCount = tasks.length;
  addTask({ title: 'Test Task', priority: 'high' });
  
  if (tasks.length === initialCount + 1) {
    console.log('✅ Test passed');
  } else {
    console.error('❌ Test failed');
  }
}
```

## 📝 Соглашения о коде

### Именование переменных
- Используйте camelCase для переменных и функций
- Используйте UPPER_CASE для констант
- Используйте kebab-case для CSS классов

### Форматирование
- 2 пробела для отступа
- Максимум 100 символов на строку
- Группируйте связанный код

### Комментарии
```javascript
// Одно-строчные комментарии для пояснения

/**
 * Многострочный комментарий для документации функций
 * @param {type} paramName - Описание параметра
 * @returns {type} Описание возвращаемого значения
 */
function importantFunction(paramName) {
  // Реализация
}
```

## 🐛 Отладка

### Браузерные DevTools

```javascript
// Откройте консоль браузера (F12) и используйте:
console.log('Debug info:', variable);
debugger; // Пауза выполнения
```

### Логирование ошибок

```javascript
window.addEventListener('error', (e) => {
  console.error('Global error:', e);
  // Отправить на сервер логирования
});
```

## 📚 Дополнительные ресурсы

- [MDN Web Docs](https://developer.mozilla.org)
- [HTML5 Specification](https://html.spec.whatwg.org)
- [CSS Grid Guide](https://css-tricks.com/snippets/css/complete-guide-grid/)
- [JavaScript ES6+](https://javascript.info)
- [Web APIs](https://developer.mozilla.org/en-US/docs/Web/API)

## 📞 Контакты и поддержка

Для вопросов по разработке создавайте Issues в репозитории.

---

*Документация актуальна для версии 1.3*
