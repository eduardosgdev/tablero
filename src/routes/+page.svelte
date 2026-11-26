
<script>
    const juegos = [
        {
            nombre: "Plim Plim",
            ruta: "/plimplim",
            imagen: "/img/plim.webp",
            color: "bg-sky-100",
            texto: "text-sky-800"
        },
        {
            nombre: "Peppa",
            ruta: "/peppa",
            imagen: "/img/pepa.webp",
            color: "bg-rose-100",
            texto: "text-rose-800"
        },
        {
            nombre: "Granja Senón",
            ruta: "/granjasenon",
            imagen: "/img/zenon.webp",
            color: "bg-lime-100",
            texto: "text-lime-800"
        },
        {
            nombre: "Molang",
            ruta: "/molang",
            imagen: "/img/molan.png",
            color: "bg-purple-500",
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

<main class="h-full w-full box-border overflow-hidden bg-gradient-to-b from-sky-100 via-white to-purple-50 p-3">
    <section class="mx-auto flex h-auto max-w-5xl flex-col justify-center gap-4 ">
        <header class="text-center">
            <h1 class="text-4xl sm:text-5xl font-black text-sky-700">
                ¡Vamos a jugar!
            </h1>

            <p class="text-lg text-gray-500 font-medium">
                Elige tu juego favorito
            </p>
        </header>
        <div class="grid grid-cols-2 items-center justify-items-center gap-4 sm:gap-6">

            {#each juegos as juego}
    <a
        href={juego.ruta}
        class={`group flex h-64 w-full  items-center justify-center
        overflow-hidden rounded-3xl border-4 border-white
        sm:h-50
        ${juego.color}
        shadow-md transition-all duration-300
        hover:-translate-y-2 hover:shadow-xl
        active:scale-95`}
    >
        <img
            src={juego.imagen}
            alt={juego.nombre}
            class="h-full w-full object-cover transition-transform duration-300 group-hover:scale-110"
        />
    </a>
{/each}

        </div>

    </section>
</main>
