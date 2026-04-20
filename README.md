# Tutorial React Router DOM

Tutorial to configure:  
Previous + React Router DOM

- IMPORTANTE !  
  Esse tutorial é uma continuação da configuração do front-end usando React com TypeScript usando o Vite.  
  Antes deve ser concluído esse outro tutorial, para instalando e configurando o Vite, Prettier, etc:  
  [Tutorial React + Code Format](https://github.com/rramires/fe1_react_code-format/blob/master/README.md)

---

### Instalação do React Router DOM

1 - Instale o React Router DOM:  
[React Router Docs](https://reactrouter.com/home) - Check Installation

```sh
pnpm add react-router
```

2 - Adicione uma pasta **pages** e dentro dela outras duas pastas **unauth** e **app**:

```sh
mkdir src/pages
mkdir src/pages/unauth
mkdir src/pages/app
```

3 - Crie uma página dentro de **pages/app** chamada **home.tsx**, contendo:

```js
export function Home() {
	return <h2>Home Page</h2>
}
```

4 - Crie uma página dentro de **pages/unauth** chamada **sign-in.tsx**, contendo:

```js
export function SignIn() {
	return <h2>SignIn Page</h2>
}
```

5 - Crie um arquivo chamado **routes.tsx** em **src** e adicione:

```js
import { createBrowserRouter } from 'react-router'

import { Home } from './pages/app/home'
import { SignIn } from './pages/unauth/sign-in'

export const router = createBrowserRouter([
	{
		path: '/',
		element: <Home />,
	},
	{
		path: '/sign-in',
		element: <SignIn />,
	},
])
```

6 - No arquivo **app.tsx** adicione, substituindo o Hello World:

```js
// adicione os imports
import { RouterProvider } from 'react-router'

import { router } from './routes'

export function App() {
	// substitua o Hello World
	return <RouterProvider router={router} />
}
```

7 - Rode para verificar:

```sh
pnpm dev
```

- Perceba que entrando em http://localhost:3001/ que é o "/",  
  aparece o conteúdo da página Home: **"Home Page!"**
- Troque a URL por http://localhost:3001/sign-in e veja carregar  
  a outra página SignIn: **"SignIn Page!"**


8 - Comite como:

```sh
git add .
git commit -m "feat: set up routing and add initial pages"
git push
```

---

### Troca de conteúdo em local determinado do Layout

1 - Adicione uma pasta **\_layouts** em **pages**:

```sh
mkdir src/pages/_layouts
```

- Nela vamos criar 2 layouts básicos para exemplificar.  
  Um para a área aberta e outro para a área logada.

2 - Na pasta **\_layouts** crie um aquivo **app-layout.tsx** e adicione:

```js
import { Outlet } from 'react-router'

export function AppLayout() {
	return (
		<>
			<header>
				<h1>AppLayout Header</h1>
			</header>
			<main>
				{/* Content will change here */}
				<Outlet />
			</main>
			<footer>
				<p>AppLayout Footer</p>
			</footer>
		</>
	)
}
```

3 - Na pasta **\_layouts** crie um aquivo **unauth-layout.tsx** e adicione:

```js
import { Outlet } from 'react-router'

export function UnauthLayout() {
	return (
		<>
			<header>
				<h1>UnauthLayout Header</h1>
			</header>
			<main>
				{/* Content will change here */}
				<Outlet />
			</main>
			<footer>
				<p>UnauthLayout Footer</p>
			</footer>
		</>
	)
}
```

4 - Modifique o arquivo **routes.tsx** para criar a nova estrutura de rotas:

```js
// adicione
import { AppLayout } from './pages/_layouts/app-layout'
import { UnauthLayout } from './pages/_layouts/unauth-layout'

export const router = createBrowserRouter([
	// remova
	/* {
		path: '/',
		element: <Home />,
	},
	{
		path: '/sign-in',
		element: <SignIn />,
	}, */
	// adicione
	{
		path: '/',
		element: <AppLayout />,
		children: [{ index: true, element: <Home /> }],
	},
	{
		path: '/sign-in',
		element: <UnauthLayout />,
		children: [{ index: true, element: <SignIn /> }],
	},
])
```

- Index = true no elemento children, significa pegae a mesmo path do elemento pai.  
  No caso **/** e **/sign-in**, nas respectivas sub-páginas. Caso necessite que carregue só quando for usado um path diferente, remova **index: true** e adicione **path: '/alguma-coisa'**.
- Perceba que entrando em http://localhost:3001/ que é o "/",
  aparece o conteúdo da página AppLayout, e carrega no local que foi adicionado o Outlet o conteúdo de Home: "Home Page!"

    Troque a URL por http://localhost:3001/sign-in e veja carregar
    a outra página UnauthLayout e no local do Outlet o conteúdo de SignIn: "SignIn Page!"

- Podemos ter várias sub-páginas usando sempre: **path, element e children** e usando o **Outlet** dentro delas para carregar o conteúdo desejado no local que quisermos, ex:

```js
// http://localhost:3001/sign-in/other-route
children: [
	{
		path: '/sign-in',
		element: <SignIn />,
		children: [{ index: true /* OR */ path: '/other-route', element: <OtherSubPage /> }],
	},
]
// use Outlet in SignIn page to load OtherSubPage
```

5 - Comite como:

```sh
git add .
git commit -m "feat: add AppLayout and UnauthLayout components for routing"
git push
```

---

### Páginas de quando for usada uma rota inválida e de erro

1 - Adicione um arquivo chamado **e404.tsx** em **pages**, contendo:

```js
export function NotFound() {
	return (
		<div>
			<h1>404 - Página não encontrada</h1>
			<h3>
				Voltar para a <a href='/'>página inicial</a>
			</h3>
		</div>
	)
}
```

2 - Adicione como última rota em **routes.tsx**:

```js
{
	path: '*',
	element: <NotFound />,
},
```

- Assim vai passar por todas as rotas, e se não encontrar nenhuma exibe essa.

3 - Adicione um arquivo chamado **error.tsx** em **pages**, contendo:

```js
import { useRouteError } from 'react-router'

interface RouteError {
	statusText?: string
	message?: string
}

export function ErrorPage() {
	const error = useRouteError() as RouteError
	console.error(error)

	return (
		<div id='error-page'>
			<h1>Oops!</h1>
			<p>Desculpe, ocorreu um erro.</p>
			<p>
				<i>{error.statusText || error.message}</i>
			</p>
		</div>
	)
}
```

4 - Adicione na primeira rota **/** em **routes.tsx** e mova as 2 rotas existentes para dentro do **children** dessa, mantendo a rota **\*** not found, por último:

```js
{
	path: '/',
	errorElement: <ErrorPage />,
	children: [ /* Mova as 2 rotas existentes para cá */ ],
},
```

O arquivo final ficará assim:

```js
import { createBrowserRouter } from 'react-router'

import { AppLayout } from './pages/_layouts/app-layout'
import { UnauthLayout } from './pages/_layouts/unauth-layout'
import { Home } from './pages/app/home'
import { NotFound } from './pages/e404'
import { ErrorPage } from './pages/error'
import { SignIn } from './pages/unauth/sign-in'

export const router = createBrowserRouter([
	{
		path: '/',
		errorElement: <ErrorPage />,
		children: [
			{
				path: '/',
				element: <AppLayout />,
				children: [{ index: true, element: <Home /> }],
			},
			{
				path: '/sign-in',
				element: <UnauthLayout />,
				children: [{ index: true, element: <SignIn /> }],
			},
		],
	},
	{
		path: '*',
		element: <NotFound />,
	},
])
```

Também é possível usar a sintaxe XML. No caso ficaria assim:

```js
import {
	createBrowserRouter,
	createRoutesFromElements,
	Route,
} from 'react-router'

import { AppLayout } from './pages/_layouts/app'
import { UnauthLayout } from './pages/_layouts/unauth'
import { Home } from './pages/app/home'
import { SignIn } from './pages/unauth/sign-in'
import { NotFound } from './pages/e404'
import { ErrorPage } from './pages/error'

export const router = createBrowserRouter(
	createRoutesFromElements(
		<Route path='/' errorElement={<ErrorPage />}>
			<Route path='/' element={<AppLayout />}>
				<Route index element={<Home />} />
			</Route>
			<Route path='/sign-in' element={<UnauthLayout />}>
				<Route index element={<SignIn />} />
			</Route>
			<Route path='*' element={<NotFound />} />
		</Route>,
	),
)
```

5 - Para testar, basta disparar um erro fictício de alguma página, ex:

```js
export function SignIn() {
	throw new Error('Simulação de erro na SignIn')
	return <h2>SignIn Page!</h2>
}
// ou
export function Home() {
	throw new Error('Simulação de erro na Home')
	return <h2>Home Page!</h2>
}
```

- De refresh, acesse essas rotas e veja os erros.  
  Não esqueça de removê-los depois.

6 - Comite como:

```sh
git add .
git commit -m "feat: add NotFound and ErrorPage components, update routing for error handling"
git push
```

---

### Alterando o título das páginas

- O **React 19** passou a suportar nativamente o hoisting de tags `<title>`, `<meta>` e `<link>` para o `<head>`, tornando pacotes como o `react-helmet-async` desnecessários para a maioria dos casos.  
  O `titleTemplate` do Helmet **não funciona no React 19**, pois as tags são renderizadas diretamente como elementos JSX.
- Vamos criar nossos próprios componentes para replicar o comportamento do `titleTemplate` usando Context API do React.

1 - Crie a pasta **components** em **src** e dentro dela a pasta **title**:

```sh
mkdir src/components/title
```

2 - Crie o arquivo **title-context.ts** em **src/components/title**, contendo:

```js
import { createContext } from 'react'

interface TitleContextValue {
	titleTemplate: string
	defaultTitle: string
}

export const TitleContext = createContext<TitleContextValue>({
	titleTemplate: '%s',
	defaultTitle: '',
})
```

- Este arquivo é um `.ts` (sem JSX) pois exporta apenas o context, não um componente.  
  Separar context de componentes é necessário para o **Fast Refresh** funcionar corretamente.

3 - Crie o arquivo **title-provider.tsx** em **src/components/title**, contendo:

```js
import type { ReactNode } from 'react'

import { TitleContext } from './title-context'

interface TitleProviderProps {
	titleTemplate?: string
	defaultTitle?: string
	children: ReactNode
}

export function TitleProvider({
	titleTemplate = '%s',
	defaultTitle = '',
	children,
}: TitleProviderProps) {
	return (
		<TitleContext.Provider value={{ titleTemplate, defaultTitle }}>
			{children}
		</TitleContext.Provider>
	)
}
```

4 - Crie o arquivo **page-title.tsx** em **src/components/title**, contendo:

```js
import { useContext } from 'react'

import { TitleContext } from './title-context'

interface PageTitleProps {
	title?: string
}

export function PageTitle({ title }: PageTitleProps) {
	const { titleTemplate, defaultTitle } = useContext(TitleContext)
	const resolved = title ? titleTemplate.replace('%s', title) : defaultTitle
	return <title>{resolved}</title>
}
```

- O **%s** no `titleTemplate` será substituído pelo `title` passado em cada página.  
  Se nenhum `title` for passado, usa o `defaultTitle`.

5 - No **App.tsx**, envolva o `RouterProvider` com o `TitleProvider`:

```js
import { RouterProvider } from 'react-router'

import { TitleProvider } from './components/title/title-provider'
import { router } from './routes'

export function App() {
	return (
		<TitleProvider
			titleTemplate='%s | Vite+RR-DOM'
			defaultTitle='Vite+RR-DOM'
		>
			<RouterProvider router={router} />
		</TitleProvider>
	)
}
```

6 - Nas páginas **home.tsx** e **sign-in.tsx**, adicione o componente **PageTitle**:

Em Home:

```js
import { PageTitle } from '@components/title/page-title'

export function Home() {
	return (
		<>
			<PageTitle title='Home' />
			<h2>Home Page!</h2>
		</>
	)
}
```

Em SignIn:

```js
import { PageTitle } from '@/components/title/page-title'

export function SignIn() {
	return (
		<>
			<PageTitle title='Sign In' />
			<h2>SignIn Page!</h2>
		</>
	)
}
```

7 - Rode para verificar:

```sh
pnpm dev
```

- Perceba os títulos mudando, conforme navega entre as rotas **/** e **/sign-in**.

8 - Adicione também nas páginas **e404.tsx** e **error.tsx**, usando um fragment na raiz do return para envolver o conteúdo já existente:

```js
import { PageTitle } from '@/components/title/page-title'

// e404.tsx
export function NotFound() {
	return (
		<>
			<PageTitle title='Not Found' />
			<div>// ...</div>
		</>
	)
}
```

```js
import { PageTitle } from '@/components/title/page-title'

// error.tsx
export function ErrorPage() {
	// ...
	return (
		<>
			<PageTitle title='Error' />
			<div id='error-page'>// ...</div>
		</>
	)
}
```

9 - Comite como:

```sh
git add .
git commit -m "feat: implement title management with TitleProvider and PageTitle components"
git push
```