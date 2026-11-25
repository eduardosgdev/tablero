
<script>
    const personajes = [
        { nombre: "plimplim", color: "bg-sky-300" },
        { nombre: "cangrejo", color: "bg-orange-300" },
        { nombre: "nesho", color: "bg-pink-300" },
        { nombre: "pam", color: "bg-yellow-300" },
        { nombre: "wichiwi", color: "bg-purple-300" },
        { nombre: "acuarela", color: "bg-green-300" },
        { nombre: "meili", color: "bg-red-600" },
        { nombre: "hoggie", color: "bg-blue-600" },
    ];

    let personajesMezclados = $state([...personajes]);

    let mostrarModal = $state(false);

    let personajeObjetivo = $state(
        personajes[Math.floor(Math.random() * personajes.length)]
    );

    let resultado = $state("");
    let aciertos = $state(0);

    // Seleccionar siguiente personaje
    function siguientePersonaje() {
    let disponibles = personajes.filter(
        personaje => personaje.nombre !== personajeObjetivo.nombre
    );

    personajeObjetivo =
        disponibles[Math.floor(Math.random() * disponibles.length)];

    mezclarPersonajes();

    resultado = "";
}

    // Comprobar selección
    function seleccionarPersonaje(personaje) {
        if (mostrarModal) return;

        if (personaje.nombre === personajeObjetivo.nombre) {
            resultado = "correcto";
            aciertos++;
            mostrarModal = true;

            setTimeout(() => {
                mostrarModal = false;
                siguientePersonaje();
            }, 1800);

        } else {
            resultado = "incorrecto";
            mostrarModal = true;

            setTimeout(() => {
                mostrarModal = false;
                resultado = "";
            }, 1300);
        }
    }

    // Reproducir pregunta
    function escucharPregunta() {
    if (typeof window === "undefined") return;

    window.speechSynthesis.cancel();

    const pregunta = new SpeechSynthesisUtterance(
        `¿Dónde está ${personajeObjetivo.nombre}?`
    );

    // Voz en español
    pregunta.lang = "es-ES";

    // Tono más agudo y alegre
    pregunta.rate = 0.95;
    pregunta.pitch = 1.4;
    pregunta.volume = 1;

    // Buscar una voz femenina en español
    const voces = window.speechSynthesis.getVoices();

    const vozFemenina = voces.find(voz =>
        voz.lang.toLowerCase().startsWith("es") &&
        /female|femenina|helena|laura|monica|lucia/i.test(voz.name)
    );

    if (vozFemenina) {
        pregunta.voice = vozFemenina;
    }

    window.speechSynthesis.speak(pregunta);
}

    function mezclarPersonajes() {
    personajesMezclados = [...personajes].sort(
        () => Math.random() - 0.5
    );
}
</script>

<svelte:head>
    <title>Encuentra los personajes | Plim Plim</title>
</svelte:head>

<main class="min-h-screen bg-gradient-to-b from-sky-100 via-white to-purple-50 p-4 sm:p-8">

    <section class="mx-auto max-w-6xl">

        <!-- PREGUNTA -->
        <div class="mb-8 rounded-3xl bg-white p-6 sm:p-10 text-center shadow-lg">

            <p class="mb-3 text-sm font-bold uppercase tracking-widest text-sky-500">
                ¡Vamos a jugar!
            </p>

            <h1 class="text-3xl sm:text-5xl font-black text-sky-800">
                ¿Dónde está
                <span class="text-orange-500 capitalize">
                    {personajeObjetivo.nombre}
                </span>?
            </h1>

            <button
                onclick={escucharPregunta}
                class="mt-5 rounded-full bg-sky-500 px-7 py-3
                font-bold text-white shadow-md transition
                hover:bg-sky-600 active:scale-95"
            >
                🔊 Escuchar pregunta
            </button>

            <div class="mt-5 text-sm font-bold text-gray-400">
                ⭐ Aciertos: {aciertos}
            </div>

        </div>

        <!-- GRID DE PERSONAJES -->
        <div class="grid grid-cols-2 md:grid-cols-4 gap-4 sm:gap-6">

            {#each personajesMezclados as personaje (personaje.nombre)}

                <button
                    onclick={() => seleccionarPersonaje(personaje)}
                    disabled={mostrarModal}
                    class="group flex min-h-44 flex-col items-center
                    justify-center overflow-hidden rounded-3xl
                    border-4 border-white p-3 shadow-lg
                    transition-all duration-300
                    hover:-translate-y-2 hover:scale-[1.02]
                    hover:shadow-xl active:scale-95
                    disabled:cursor-default
                    {personaje.color}"
                >

                    <div class="flex h-28 w-full items-center justify-center sm:h-40">

                        <img
                            src={`/img/plim/${personaje.nombre}.png`}
                            alt={personaje.nombre}
                            class="h-full w-full object-contain
                            transition-transform duration-300
                            group-hover:scale-110"
                        />

                    </div>

                    <span class="mt-3 rounded-full bg-white/90 px-4 py-1
                        text-sm font-black capitalize text-gray-800 shadow-sm">
                        {personaje.nombre}
                    </span>

                </button>

            {/each}

        </div>

    </section>

    <!-- MODAL -->
    {#if mostrarModal}

        <div
            class="fixed inset-0 z-50 flex items-center justify-center
            bg-sky-950/60 p-5 backdrop-blur-sm"
        >

            {#if resultado === "correcto"}

                <div class="w-full max-w-sm rounded-[2rem]
                    border-8 border-yellow-300
                    bg-gradient-to-br from-yellow-100 via-green-100 to-sky-100
                    p-8 text-center shadow-2xl animate-bounce">

                    <div class="mb-3 text-7xl">
                        🎉
                    </div>

                    <h2 class="text-3xl font-black text-green-600">
                        ¡MUY BIEN!
                    </h2>

                    <p class="mt-3 text-lg font-bold text-sky-800">
                        ¡Has encontrado a {personajeObjetivo.nombre}!
                    </p>

                    <div class="mt-5 text-4xl">
                        ⭐ 🥳 ⭐
                    </div>

                </div>

            {:else if resultado === "incorrecto"}

                <div class="w-full max-w-sm rounded-[2rem]
                    border-8 border-orange-300
                    bg-gradient-to-br from-orange-100 via-yellow-100 to-pink-100
                    p-8 text-center shadow-2xl">

                    <div class="mb-3 text-7xl">
                        🤗
                    </div>

                    <h2 class="text-3xl font-black text-orange-600">
                        ¡CASI!
                    </h2>

                    <p class="mt-3 text-lg font-bold text-pink-700">
                        ¡Inténtalo otra vez! Tú puedes.
                    </p>

                    <div class="mt-5 text-4xl">
                        💪 🌈 ❤️
                    </div>

                </div>

            {/if}

        </div>

    {/if}

</main>

