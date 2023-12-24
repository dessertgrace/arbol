<script>
	import { page } from '$app/stores';
	import logo from '$lib/images/svelte-logo.svg';
	import github from '$lib/images/github.svg';
</script>

<header>
	<nav class="namehead">
		<ul>
			<li aria-current={$page.url.pathname === '/' ? 'page' : undefined}>
				<a href="/">Arbol</a>
			</li>
			<li aria-current={$page.url.pathname === '/about' ? 'page' : undefined}>
				<a href="/">About</a>
			</li>
		</ul>
	</nav>
</header>

<style>

	nav {
		display: flex;
		justify-content: space-between;
	}

	.namehead {
		display: flex;
		justify-content: space-between;
		height: 100%;
		align-items: center;
		padding: 0 0.5rem;
		color: var(--color-text);
		font-weight: 700;
		font-size: 0.8rem;
		text-transform: uppercase;
		letter-spacing: 0.1em;
		text-decoration: none;
		transition: color 0.2s linear;
	}

	.namehead ul {
		padding: 0.4rem 1rem;
		width: 100%;
		justify-content: space-between;
	}

	.namehead li {
		padding: 0;
		margin: 0;
	}

	.namehead:hover {
		color: var(--color-theme-1);
	}
</style>
