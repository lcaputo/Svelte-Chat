<script>
	let register = false;
	let login_email = '';
	let login_password = '';
	let register_name = '';
    let register_email = '';
    let register_password = '';

	function handleSubmitLogin() {
		const formData = {
			login_email,
			login_password
		};
        fetch('http://localhost:1323/login', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                email: login_email,
                password: login_password
            })
        }).then(res => res.json())
        .then(res => {
            if (res.status === 200) {
                localStorage.setItem('token', res.data);
                window.location.href = '/chat';
            }
        })
    }
    function handleSubmitRegister() {
        const formData = {
            register_name,
            register_email,
            register_password
        };
        alert(JSON.stringify(formData));
    }
</script>

<div class="flex justify-center items-center h-screen">
	<div class="w-full max-w-md">
		{#if register}
			<h1 class="text-3xl font-bold text-center mb-4">Registrarse</h1>
            <form on:submit|preventDefault={handleSubmitRegister} class="bg-white shadow-lg rounded px-8 py-6">
				<div class="mb-4">
					<label class="block text-gray-700 font-bold mb-2" for="username"> Full name </label>
					<input
						class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
						id="register_name"
						type="text"
						bind:value={register_name}
					/>
				</div>
                <div class="mb-4">
					<label class="block text-gray-700 font-bold mb-2" for="password"> Email </label>
					<input
						class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline"
						id="register_email"
						type="email"
						bind:value={register_email}
					/>
				</div>
				<div class="mb-6">
					<label class="block text-gray-700 font-bold mb-2" for="password"> Password </label>
					<input
						class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline"
						id="register_password"
						type="password"
						bind:value={register_password}
					/>
				</div>
				<div>
					<button
						class="w-full bg-cyan-500 hover:bg-cyan-600 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
						type="submit"
					>
						Registrarse
					</button>
					<hr class="my-3 border-gray-200" />
                    <button
                        class="w-full bg-gray-300 hover:bg-gray-400 text-gray-700 font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
                        type="button"
                        on:click={() => (register = false)}
					>
						Volver al login
					</button>
				</div>
			</form>
		{:else}
			<h1 class="text-3xl font-bold text-center mb-4">Iniciar sesión</h1>
			<form on:submit|preventDefault={handleSubmitLogin} class="bg-white shadow-lg rounded px-8 py-6">
				<div class="mb-4">
					<label class="block text-gray-700 font-bold mb-2" for="username"> Email </label>
					<input
						class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
						id="login_email"
						type="email"
						bind:value={login_email}
					/>
				</div>
				<div class="mb-6">
					<label class="block text-gray-700 font-bold mb-2" for="password"> Password </label>
					<input
						class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline"
						id="login_password"
						type="password"
						bind:value={login_password}
					/>
				</div>
				<div>
					<button
						class="w-full bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
						type="submit"
					>
						Iniciar sesión
					</button>
					<hr class="my-3 border-gray-200" />
					<button
						class="w-full bg-gray-300 hover:bg-gray-400 text-gray-700 font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
						type="button"
                        on:click={() => (register = true)}
					>
						Registrarse
					</button>
				</div>
			</form>
		{/if}
	</div>
</div>
