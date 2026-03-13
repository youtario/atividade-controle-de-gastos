<template>
    <div class="app">

        <header class="header">
            <h1>Controle de Gastos</h1>
            <div class="total-badge">
                R$ {{ total.toFixed(2) }}
            </div>
        </header>

        <div class="tabs">
            <button
                v-for="tab in tabs"
                :key="tab.value"
                class="tab"
                :class="{ active: filter === tab.value }"
                @click="filter = tab.value"
            >
                {{ tab.label }}
            </button>
        </div>

        <div class="list">
            <div v-if="filtered.length === 0" class="empty">
                Nenhuma despesa aqui 
            </div>
            <div
                v-for="item in filtered"
                :key="item.id"
                class="expense-item"
            >
                <div class="expense-icon">{{ categoryIcon(item.category) }}</div>
                <div class="expense-info">
                    <span class="expense-title">{{ item.title }}</span>
                    <span class="expense-cat">{{ categoryLabel(item.category) }}</span>
                </div>
                <div class="expense-right">
                    <span class="expense-value">R$ {{ Number(item.value).toFixed(2) }}</span>
                    <button class="delete-btn" @click="removeExpense(item.id)">✕</button>
                </div>
            </div>
        </div>

        <div class="clear-wrap" v-if="expenses.length > 0">
            <button class="clear-btn" @click="clearAll">Limpar tudo</button>
        </div>

        <div class="add-form" :class="{ open: formOpen }">
            <div class="form-handle" @click="formOpen = !formOpen">
                <span v-if="!formOpen">＋ Nova despesa</span>
                <span v-else>▼ Fechar</span>
            </div>

            <div class="form-body" v-if="formOpen">
                <input
                    v-model="title"
                    class="input"
                    placeholder="Descrição"
                    type="text"
                />
                <input
                    v-model="value"
                    class="input"
                    placeholder="Valor (ex: 12.50)"
                    type="number"
                    inputmode="decimal"
                    step="0.01"
                    min="0"
                />
                <div class="cat-select">
                    <button
                        v-for="cat in categoryOptions"
                        :key="cat.value"
                        class="cat-btn"
                        :class="{ active: category === cat.value }"
                        @click="category = cat.value"
                    >
                        {{ cat.icon }} {{ cat.label }}
                    </button>
                </div>
                <button class="add-btn" @click="addExpense">Adicionar</button>
            </div>
        </div>

    </div>
</template>

<script setup>
import { computed, ref } from 'vue';

const expenses = ref([
    { id: 1, title: 'Café', value: 6, category: 'food' },
    { id: 2, title: 'Ônibus', value: 4.5, category: 'transport' },
    { id: 3, title: 'Lanche', value: 12, category: 'food' },
]);

const title = ref('');
const value = ref('');
const category = ref('other');
const filter = ref('all');
const formOpen = ref(false);

const tabs = [
    { value: 'all', label: 'Tudo' },
    { value: 'food', label: 'Comida' },
    { value: 'transport', label: 'Transporte' },
    { value: 'passeio', label: 'Passeio' },
    { value: 'other', label: 'Outros' },
];

const categoryOptions = [
    { value: 'food', label: 'Comida', icon: '🍔' },
    { value: 'transport', label: 'Transporte', icon: '🚌' },
     { value: 'passeio', label: 'Passeio', icon: '🗿' },
    { value: 'other', label: 'Outros', icon: '📦' },
];

function categoryIcon(cat) {
    const found = categoryOptions.find(c => c.value === cat);
    return found ? found.icon : '📦';
}

function categoryLabel(cat) {
    const found = categoryOptions.find(c => c.value === cat);
    return found ? found.label : 'Outros';
}

const filtered = computed(() => {
    if (filter.value === 'all') return expenses.value;
    return expenses.value.filter(item => item.category === filter.value);
});

const total = computed(() => {
    return expenses.value.reduce((sum, item) => sum + Number(item.value || 0), 0);
});

function addExpense() {
    if (!title.value.trim() || !value.value) {
        alert('Preencha a descrição e o valor');
        return;
    }
    expenses.value.push({
        id: Date.now(),
        title: title.value.trim(),
        value: parseFloat(value.value),
        category: category.value || 'other',
    });
    title.value = '';
    value.value = '';
    category.value = 'other';
    formOpen.value = false;
}

function removeExpense(id) {
    expenses.value = expenses.value.filter(item => item.id !== id);
}

function clearAll() {
    if (!confirm('Tem certeza que quer apagar tudo? :( ')) return;
    expenses.value = [];
}
</script>