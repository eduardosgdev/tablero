
<script>
    const juegos = [
        {
            nombre: "Plim Plim",
            ruta: "/plimplim",
            imagen: "/images/plimplim.webp",
            color: "bg-sky-100",
            texto: "text-sky-800"
        },
        {
            nombre: "Peppa",
            ruta: "/peppa",
            imagen: "/images/peppa.webp",
            color: "bg-rose-100",
            texto: "text-rose-800"
        },
        {
            nombre: "Granja Senón",
            ruta: "/granjasenon",
            imagen: "/images/granjasenon.webp",
            color: "bg-lime-100",
            texto: "text-lime-800"
        },
        {
            nombre: "Molang",
            ruta: "/molang",
            imagen: "/images/molang.webp",
            color: "bg-purple-100",
            texto: "text-purple-800"
        }
    ];
</script>

<svelte:head>
    <title>Mis juegos | Mundo divertido</title>
    <meta
        name="description"
        content="Un mundo de juegos y diversión."
    />
</svelte:head>

<main class="min-h-screen bg-gradient-to-b from-sky-50 to-white px-5 py-10 sm:px-8">

    <section class="mx-auto max-w-5xl">

        <!-- CABECERA -->
        <header class="mb-10 text-center">
            <h1 class="text-4xl sm:text-5xl font-black text-sky-700">
                ¡Vamos a jugar!
            </h1>

            <p class="mt-3 text-lg text-gray-500 font-medium">
                Elige tu juego favorito
            </p>
        </header>

        <!-- GRID DE JUEGOS -->
        <div class="grid grid-cols-2 gap-4 sm:gap-6">

            {#each juegos as juego}
                <a
                    href={juego.ruta}
                    class="group flex flex-col items-center justify-center
                    overflow-hidden rounded-3xl border-4 border-white
                    {juego.color} p-4 sm:p-7
                    shadow-md transition-all duration-300
                    hover:-translate-y-2 hover:shadow-xl
                    active:scale-95"
                >
                    <div class="flex h-32 w-full items-center justify-center sm:h-48">
                        <img
                            src={juego.imagen}
                            alt={juego.nombre}
                            class="h-full w-full object-contain
                            transition-transform duration-300
                            group-hover:scale-110"
                        />
                    </div>

                    <h2 class="mt-4 text-center text-xl sm:text-2xl
                        font-black {juego.texto}">
                        {juego.nombre}
                    </h2>

                    <span class="mt-2 rounded-full bg-white/80
                        px-5 py-2 text-sm font-bold {juego.texto}">
                        ¡Jugar!
                    </span>
                </a>
            {/each}

        </div>

    </section>

</main>
