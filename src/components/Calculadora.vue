<script setup>
import { ref, computed, onMounted } from 'vue'

const num1 = ref(null)
const num2 = ref(null)
const operacao = ref('')
const historico = ref([])
const mounted = ref(false)

const camposVazios = computed(() => num1.value === null || num2.value === null)

const calcular = (a, b, op) => {
    switch (op) {
        case '+': return a + b
        case '-': return a - b
        case '*': return a * b
        case '/': return b !== 0 ? a / b : 'Erro'
        default: return ''
    }
}

const mensagemResultado = computed(() => {
    if (camposVazios.value || !operacao.value) return ''
    if (operacao.value === '/' && num2.value === 0) return 'Divisão por zero!'
    
    const resultado = calcular(num1.value, num2.value, operacao.value)
    const expressao = `${num1.value} ${operacao.value} ${num2.value} = ${resultado}`
    
    if (!historico.value.includes(expressao)) {
        historico.value.push(expressao)
    }
    
    return resultado
})

const limparHistorico = () => {
    historico.value.splice(0)
}

const novoCalculo = () => {
    num1.value = null
    num2.value = null
    operacao.value = ''
}

onMounted(() => {
    mounted.value = true
})
</script>

<template>
    <div class="bg-calculadora text-dark" style="min-height: 100vh;">
        <div class="container py-5">
            <transition name="fade-slide">
        <div
            v-show="mounted"
            class="mx-auto p-4 rounded shadow bg-light text-dark"
            style="max-width: 400px;">
            <h2 class="text-center mb-4 fw-bold">Calculadora Aritmética</h2>

            <input
                type="number"
                inputmode="numeric"
                v-model.number="num1"
                class="form-control mb-3"
                placeholder="Digite o primeiro número"
            />

            <input
                type="number"
                inputmode="numeric"
                v-model.number="num2"
                class="form-control mb-3"
                placeholder="Digite o segundo número"
            />

            <select
                v-model="operacao"
                class="form-select mb-3 w-50 mx-auto"
                :disabled="camposVazios"
            >
                <option disabled value="">Escolha</option>
                <option value="+">Somar</option>
                <option value="-">Subtrair</option>
                <option value="*">Multiplicar</option>
                <option value="/">Dividir</option>
            </select>

            <p class="text-center fw-semibold">Resultado: {{ mensagemResultado }}</p>

            <div v-if="historico.length" class="mt-4">
                <h5>Histórico:</h5>
                <transition-group name="fade" tag="ul" class="list-group mb-3">
                <li
                    v-for="(item, index) in historico"
                    :key="item + index"
                    class="list-group-item"
                >
                    {{ item }}
                </li>
                </transition-group>

            <button
                @click="limparHistorico"
                class="btn btn-danger w-100"
                :disabled="historico.length === 0"
            >
                Limpar Histórico
            </button>

            <button
                @click="novoCalculo"
                class="btn btn-secondary w-100 mt-2"
                :disabled="camposVazios && !operacao"
            >
                Novo Cálculo
            </button>
            </div>
        </div>
        </transition>
        </div>
    </div>
</template>

<style scoped>
.fade-slide-enter-active {
    transition: all 0.5s ease;
}
.fade-slide-enter-from {
    opacity: 0;
    transform: translateY(20px);
}
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.4s;
}
.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}
</style>