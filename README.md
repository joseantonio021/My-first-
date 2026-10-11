# riscar-todo

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  min-height: 100vh;
  font-family: Arial, sans-serif;
  background: #f3f5f4;
  color: #26352f;
}

.container {
  width: min(100% - 32px, 650px);
  margin: 40px auto;
}

header {
  background: #194d3a;
  color: white;
  padding: 28px 24px;
  border-radius: 18px;
}

.marca {
  font-size: 12px;
  letter-spacing: 2px;
  font-weight: bold;
}

h1 {
  margin-bottom: 8px;
}

header p:last-child {
  color: #e0eee6;
}

form, .painel {
  margin-top: 24px;
  padding: 22px;
  background: white;
  border-radius: 16px;
}

label {
  display: block;
  margin-bottom: 10px;
  font-weight: bold;
}

.entrada {
  display: flex;
  gap: 8px;
}

input {
  flex: 1;
  min-width: 0;
  padding: 13px;
  border: 1px solid #ccd6d0;
  border-radius: 8px;
  font-size: 16px;
}

button {
  border: 0;
  border-radius: 8px;
  padding: 12px 15px;
  background: #267653;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

button:hover {
  opacity: 0.88;
}

.resumo {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
}

.resumo h2 {
  font-size: 20px;
}

#contador {
  color: #607168;
  font-size: 13px;
}

.filtros {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin: 12px 0 20px;
}

.filtro {
  background: #e8eee9;
  color: #294b3a;
}

.filtro.ativo {
  background: #194d3a;
  color: white;
}

#lista-tarefas {
  list-style: none;
  padding: 0;
  margin: 0;
}

.tarefa {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 14px 0;
  border-bottom: 1px solid #e7ece8;
}

.tarefa span {
  flex: 1;
  overflow-wrap: anywhere;
}

.tarefa.concluida span {
  text-decoration: line-through;
  color: #829087;
}

.tarefa input {
  flex: none;
  width: 18px;
  height: 18px;
}

.excluir {
  background: #a63838;
  padding: 9px 11px;
}

#mensagem {
  color: #718078;
  text-align: center;
  padding: 14px 0;
}

footer {
  margin-top: 22px;
  text-align: center;
  color: #718078;
  font-size: 13px;
}

@media (max-width: 420px) {
  .entrada {
    flex-direction: column;
  }

  .resumo {
    align-items: flex-start;
    flex-direction: column;
  }
}
 


const formulario = document.getElementById("form-tarefa");
const campo = document.getElementById("nova-tarefa");
const lista = document.getElementById("lista-tarefas");
const mensagem = document.getElementById("mensagem");
const contador = document.getElementById("contador");
const botoesFiltro = document.querySelectorAll(".filtro");

let tarefas = [];
let filtroAtual = "todas";

function atualizarTela() {
  lista.innerHTML = "";

  const visiveis = tarefas.filter(tarefa => {
    if (filtroAtual === "pendentes") return !tarefa.concluida;
    if (filtroAtual === "concluidas") return tarefa.concluida;
    return true;
  });

  contador.textContent = `${tarefas.length} tarefa(s)`;

  visiveis.forEach(tarefa => {
    const item = document.createElement("li");
    item.className = "tarefa";

    if (tarefa.concluida) {
      item.classList.add("concluida");
    }

    const checkbox = document.createElement("input");
    checkbox.type = "checkbox";
    checkbox.checked = tarefa.concluida;
    checkbox.setAttribute("aria-label", "Concluir tarefa");

    checkbox.addEventListener("change", () => {
      tarefa.concluida = checkbox.checked;
      atualizarTela();
    });

    const texto = document.createElement("span");
    texto.textContent = tarefa.nome;

    const excluir = document.createElement("button");
    excluir.textContent = "Excluir";
    excluir.className = "excluir";
    excluir.type = "button";

    excluir.addEventListener("click", () => {
      tarefas = tarefas.filter(t => t.id !== tarefa.id);
      atualizarTela();
    });

    item.append(checkbox, texto, excluir);
    lista.appendChild(item);
  });

  mensagem.hidden = visiveis.length > 0;

  if (visiveis.length === 0) {
    mensagem.textContent = tarefas.length === 0
      ? "Você ainda não tem tarefas."
      : "Nenhuma tarefa encontrada neste filtro.";
  }
}

formulario.addEventListener("submit", evento => {
  evento.preventDefault();

  const nome = campo.value.trim();
  if (!nome) return;

  tarefas.push({
    id: Date.now() + Math.random(),
    nome,
    concluida: false
  });

  campo.value = "";
  atualizarTela();
  campo.focus();
});

botoesFiltro.forEach(botao => {
  botao.addEventListener("click", () => {
    filtroAtual = botao.dataset.filtro;

    botoesFiltro.forEach(b => {
      b.classList.toggle("ativo", b === botao);
    });

    atualizarTela();
  });
});

atualizarTela();



