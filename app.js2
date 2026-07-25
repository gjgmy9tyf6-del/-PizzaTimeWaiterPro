document.addEventListener('DOMContentLoaded', () => {
  const mainContainer = document.getElementById('mainContainer');
  const searchInput = document.getElementById('searchInput');

  // Order storage (in memory)
  let order = [];

  // Category definitions
  const categories = [
    { key: "بيتزا", icon: "🍕", name: "Pizza" },
    { key: "بيتزا فاخرة", icon: "🍕", name: "بيتزا فاخرة" },
    { key: "بيتزا بالكريمة", icon: "🍕", name: "بيتزا كريمة" },
    { key: "برجر", icon: "🍔", name: "Burgers" },
    { key: "باستا", icon: "🍝", name: "Pasta" },
    { key: "سلطات", icon: "🥗", name: "Salads" },
    { key: "مقبلات", icon: "🍟", name: "Appetizers" },
    { key: "ساندويشات", icon: "🥖", name: "ساندويشات" },
    { key: "كالزوني", icon: "🥟", name: "كالزوني" },
    { key: "مشروبات", icon: "🥤", name: "Drinks" },
    { key: "حلويات", icon: "🍰", name: "Desserts" },
    { key: "وجبات أطفال", icon: "🧒", name: "وجبات أطفال" }
  ];

  // Render home page
  function renderHome() {
    searchInput.value = '';
    searchInput.oninput = null;
    let html = '<div class="home-grid">';
    categories.forEach(cat => {
      html += `
        <div class="category-card" data-category="${cat.key}">
          <span class="icon">${cat.icon}</span>
          <span>${cat.name}</span>
        </div>`;
    });
    html += `
      <div class="category-card" id="orderCard">
        <span class="icon">🛒</span>
        <span>الطلب الحالي</span>
      </div>
      <div class="category-card" id="trainingCard">
        <span class="icon">🎓</span>
        <span>تدريب السفرجي</span>
      </div>`;
    html += '</div>';
    mainContainer.innerHTML = html;

    document.querySelectorAll('.category-card[data-category]').forEach(card => {
      card.addEventListener('click', () => {
        renderCategory(card.dataset.category);
      });
    });
    document.getElementById('orderCard').addEventListener('click', renderOrder);
    document.getElementById('trainingCard').addEventListener('click', renderTraining);
  }

  function renderCategory(categoryKey) {
    const items = MENU.filter(item => item.category === categoryKey);
    let html = `
      <button class="back-btn" onclick="renderHome()">← رجوع</button>
      <h2>${categoryKey}</h2>
      <div class="items-grid">`;
    if (items.length === 0) {
      html += '<p class="empty-state">لا توجد أصناف حالياً.</p>';
    } else {
      items.forEach(item => {
        html += `
          <div class="item-card" data-id="${item.id}">
            <img src="${item.image}" alt="${item.name}" onerror="this.src='https://via.placeholder.com/300x200?text=${encodeURIComponent(item.name)}'">
            <div class="info">
              <h3>${item.name}</h3>
              <p>${item.description || ''}</p>
            </div>
          </div>`;
      });
    }
    html += '</div>';
    mainContainer.innerHTML = html;

    document.querySelectorAll('.item-card').forEach(card => {
      card.addEventListener('click', () => {
        const id = parseInt(card.dataset.id);
        renderDetail(id);
      });
    });

    searchInput.oninput = function(e) {
      const query = e.target.value.trim().toLowerCase();
      document.querySelectorAll('.item-card').forEach(card => {
        const itemId = parseInt(card.dataset.id);
        const item = MENU.find(i => i.id === itemId);
        if (item && (item.name.toLowerCase().includes(query) || (item.description && item.description.toLowerCase().includes(query)) || item.ingredients.some(ing => ing.toLowerCase().includes(query)))) {
          card.style.display = '';
        } else {
          card.style.display = 'none';
        }
      });
    };
  }

  function renderDetail(id) {
    const item = MENU.find(i => i.id === id);
    if (!item) return;
    let html = `
      <button class="back-btn" onclick="renderCategory('${item.category}')">← رجوع للقسم</button>
      <div class="detail-container">
        <img src="${item.image}" alt="${item.name}" class="detail-img" onerror="this.src='https://via.placeholder.com/300x200?text=${encodeURIComponent(item.name)}'">
        <div class="detail-body">
          <h2>${item.name}</h2>
          <p class="description">${item.description || ''}</p>
          <h4>المكونات:</h4>
          <ul class="ingredients-list">
            ${item.ingredients.map(ing => `<li>${ing}</li>`).join('')}
          </ul>`;

    if (item.removableIngredients && item.removableIngredients.length > 0) {
      html += `<div class="modifiers">
        <h4>إزالة مكونات:</h4>
        <div class="checkbox-group" id="removableGroup">`;
      item.removableIngredients.forEach(ing => {
        html += `<label><input type="checkbox" value="${ing}"> بدون ${ing}</label>`;
      });
      html += `</div></div>`;
    }

    if (item.extras && item.extras.length > 0) {
      html += `<div class="modifiers">
        <h4>إضافات:</h4>
        <div class="checkbox-group" id="extrasGroup">`;
      item.extras.forEach(ext => {
        html += `<label><input type="checkbox" value="${ext}"> ${ext}</label>`;
      });
      html += `</div></div>`;
    }

    html += `<button class="add-order-btn" id="addToOrderBtn">إضافة للطلب</button></div></div>`;
    mainContainer.innerHTML = html;

    document.getElementById('addToOrderBtn').addEventListener('click', () => {
      const removed = Array.from(document.querySelectorAll('#removableGroup input:checked')).map(cb => cb.value);
      const extras = Array.from(document.querySelectorAll('#extrasGroup input:checked')).map(cb => cb.value);
      const existing = order.find(o => o.id === item.id && arraysEqual(o.removed, removed) && arraysEqual(o.extras, extras));
      if (existing) {
        existing.quantity += 1;
      } else {
        order.push({ id: item.id, name: item.name, category: item.category, removed, extras, quantity: 1, image: item.image });
      }
      alert('تمت الإضافة إلى الطلب');
    });
  }

  function arraysEqual(a, b) {
    if (a.length !== b.length) return false;
    const sortedA = [...a].sort();
    const sortedB = [...b].sort();
    return sortedA.every((val, idx) => val === sortedB[idx]);
  }

  function renderOrder() {
    let html = `<button class="back-btn" onclick="renderHome()">← رجوع</button><h2>🛒 الطلب الحالي</h2>`;
    if (order.length === 0) {
      html += '<p class="empty-state">لا توجد أصناف مضافة بعد.</p>';
    } else {
      html += '<div class="order-list">';
      order.forEach((item, index) => {
        const notes = generateNotes(item.removed, item.extras);
        html += `
          <div class="order-item">
            <div class="order-item-info">
              <strong>${item.name}</strong> (${item.quantity})
              ${notes ? `<div class="order-item-notes">${notes}</div>` : ''}
            </div>
            <div style="display: flex; flex-direction: column; align-items: center; gap: 8px;">
              <div class="quantity-controls">
                <button onclick="changeQuantity(${index}, -1)">−</button>
                <span>${item.quantity}</span>
                <button onclick="changeQuantity(${index}, 1)">+</button>
              </div>
              <button class="delete-btn" onclick="removeFromOrder(${index})">حذف</button>
            </div>
          </div>`;
      });
      html += '</div>';
    }
    mainContainer.innerHTML = html;
    searchInput.oninput = null;
  }

  function generateNotes(removed, extras) {
    let notes = [];
    removed.forEach(r => notes.push(`WITHOUT ${r.toUpperCase()}`));
    extras.forEach(e => notes.push(`EXTRA ${e.toUpperCase()}`));
    return notes.join('\n');
  }

  window.changeQuantity = function(index, delta) {
    if (order[index].quantity + delta <= 0) {
      order.splice(index, 1);
    } else {
      order[index].quantity += delta;
    }
    renderOrder();
  };

  window.removeFromOrder = function(index) {
    order.splice(index, 1);
    renderOrder();
  };

  function renderTraining() {
    let html = `<button class="back-btn" onclick="renderHome()">← رجوع</button><h2>🎓 تدريب السفرجي</h2>`;
    const itemsWithTraining = MENU.filter(item => item.serving || (item.suggestions && item.suggestions.length > 0));
    if (itemsWithTraining.length === 0) {
      html += '<p class="empty-state">لا توجد بيانات تدريبية حالياً.</p>';
    } else {
      itemsWithTraining.forEach(item => {
        html += `
          <div class="training-item">
            <h3>${item.name} (${item.category})</h3>
            <p><strong>المكونات:</strong> ${item.ingredients.join('، ') || 'غير محدد'}</p>
            ${item.serving ? `<p><strong>طريقة التقديم:</strong> ${item.serving}</p>` : ''}
            ${item.suggestions && item.suggestions.length > 0 ? `
              <div><strong>اقتراحات البيع:</strong>
                <div class="suggestions">${item.suggestions.map(s => `<span>${s}</span>`).join('')}</div>
              </div>` : ''}
          </div>`;
      });
    }
    mainContainer.innerHTML = html;
    searchInput.oninput = function(e) {
      const query = e.target.value.trim().toLowerCase();
      document.querySelectorAll('.training-item').forEach(card => {
        card.style.display = card.innerText.toLowerCase().includes(query) ? '' : 'none';
      });
    };
  }

  renderHome();
});
