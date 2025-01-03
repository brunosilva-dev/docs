# React Documentation

## Bundlers e Compiladores

### Compiladores
- **Babel**: [babeljs.io](https://babeljs.io)

### Bundlers
- **Webpack**: Unifica os arquivos para otimizar a entrega do código.

## Criando um Projeto com Vite

### Pré-requisitos
- Node.js instalado (versão LTS).

### Passos
1. Execute o comando: `npm create vite@latest`.
2. Após criar o projeto, instale as dependências com `npm i` ou `npm install`.
3. Para rodar o projeto, utilize o comando: `npm run dev`.

**Alterando a Porta do Servidor:**
No arquivo `vite.config.js`, adicione o seguinte:

```js
export default defineConfig({
  plugins: [react()],
  server: {
    port: 3001,
  },
});
```

## Componentes

- **Definição**: Desacoplam partes da interface, facilitando a manutenção e reutilização.
- **JSX (JavaScript + XML)**: É o formato padrão para criar componentes.

### Exemplo: Criando um Componente de Postagem

```jsx
export function Post(props) {
  return (
    <div>
      <strong>{props.author}</strong>
      <p>{props.content}</p>
    </div>
  );
}

export function App() {
  return (
    <div>
      <Post
        author="Bruno Silva"
        content="Teste de passagem das propriedades dentro do elemento!"
      />
    </div>
  );
}
```

## CSS

### CSS Modules
- **Documentação**: [CSS Modules GitHub](https://github.com/css-modules/css-modules)
- Arquivos devem seguir o formato `nome.module.css`.
- Exemplo de uso:

```js
import s from "./Header.module.css";

export function Header() {
  return (
    <header className={s.header}>
      <strong>Ignite Feed</strong>
    </header>
  );
}
```

### CSS Global
Embora o CSS global seja suportado, o uso de CSS Modules é recomendado para evitar conflitos de estilo.

### Importando Estilos

```jsx
import "./styles.css";

export function App() {
  return (
    <div>
      <Post
        author="Bruno Silva"
        content="Teste de passagem das propriedades dentro do elemento!"
      />
    </div>
  );
}
```

## Ferramentas e Bibliotecas

### Figma
- Projeto de referência: [Ignite Feed](https://www.figma.com/community/file/1194740072221053209/Ignite-Feed)

### Bibliotecas
- **Icons**: [Phosphor Icons](https://phosphoricons.com) - Instalação: `npm i phosphor-react`
- **Manipulação de Datas**: [date-fns](https://date-fns.org) - Instalação: `npm i date-fns`

Exemplo de uso com `date-fns`:

```jsx
import { format, formatDistanceToNow } from "date-fns";
import ptBR from "date-fns/locale/pt-BR";

const publishedAtFormatted = format(
  publishedAt,
  "d 'de' LLLL 'às' HH:mm'h'",
  { locale: ptBR }
);

const publishedDateRelativeToNow = formatDistanceToNow(publishedAt, {
  locale: ptBR,
  addSuffix: true,
});
```

## Estado (State)

Estados são variáveis que o componente monitora e atualiza dinamicamente.

### Exemplo: Gerenciando Comentários

```jsx
const [comments, setComments] = useState(["Primeiro comentário"]);

function handleCreateNewComment(event) {
  event.preventDefault();
  setComments([...comments, `Novo comentário ${comments.length + 1}`]);
}

return (
  <div className="commentList">
    {comments.map((comment, index) => (
      <Comment key={index} content={comment} />
    ))}
  </div>
);
```

### Key
Ao renderizar listas, sempre utilize a propriedade `key` para identificar os elementos de forma única.

```jsx
comments.map((comment, index) => (
  <Comment key={index} content={comment} />
));
