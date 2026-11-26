
<script>
    let saltos = $state(0);

    function saltar() {
        saltos++;
    }

    function manejarTecla(event) {
        if (event.code === "Space" && !event.repeat) {
            event.preventDefault();
            saltar();
        }
    }
</script>

<svelte:window onkeydown={manejarTecla} />

<main class="min-h-screen w-full overflow-hidden bg-sky-100 p-3 sm:p-6">

    <section class="mx-auto flex max-w-7xl flex-col gap-4">

        <!-- CABECERA -->

        <header class="relative z-50 flex items-center justify-between rounded-3xl bg-white p-4 shadow-md">

            <div>
                <h1 class="text-2xl font-black text-pink-500 sm:text-4xl">
                    ¡Molang y Piu Piu!
                </h1>

                <p class="font-bold text-gray-500">
                    ¡Ayuda a Piu Piu a saltar!
                </p>
            </div>

            <div class="rounded-2xl bg-yellow-100 px-4 py-2 text-center">
                <span class="text-xs font-black text-yellow-700">
                    SALTOS
                </span>

                <p class="text-3xl font-black text-yellow-600">
                    {saltos}
                </p>
            </div>

        </header>


        <!-- ESCENARIO -->

        <div class="escenario relative h-[430px] w-full overflow-hidden rounded-3xl border-4 border-white shadow-xl sm:h-[520px]">

            <!-- CIELO -->

            <div class="absolute inset-0 z-0 bg-gradient-to-b from-sky-300 to-sky-100"></div>


            <!-- MONTAÑAS -->

            <div class="absolute inset-x-0 top-0 z-10 h-[75%] overflow-hidden">
                <div class="paisaje paisaje-lento">
                    <img src="/img/piupiu/mountain.png" alt="" />
                    <img src="/img/piupiu/mountain.png" alt="" />
                </div>
            </div>


            <!-- COLINAS -->

            <div class="absolute inset-x-0 bottom-[25%] z-20 h-[48%] overflow-hidden">
                <div class="paisaje paisaje-medio">
                    <img src="/img/piupiu/colinas.png" alt="" />
                    <img src="/img/piupiu/colinas.png" alt="" />
                </div>
            </div>


            <!-- CAMINO -->

            <div class="absolute inset-x-0 bottom-0 z-30 h-[32%] overflow-hidden">
                <div class="paisaje paisaje-rapido">
                    <img src="/img/piupiu/camino.png" alt="" />
                    <img src="/img/piupiu/camino.png" alt="" />
                </div>
            </div>


            <!-- PERSONAJES -->

            <div class="absolute bottom-[5%] left-0 z-40 flex w-full items-end justify-center gap-12">

                <!-- MOLANG DETRÁS -->

                <img
                    src="/img/piupiu/molang.png"
                    alt="Molang"
                    class="molang"
                />

                <!-- PIU PIU DELANTE -->

                {#key saltos}
    <img
        src="/img/piupiu/piupiu.png"
        alt="Piu Piu en bicicleta"
        class="piupiu"
    />
{/key}

            </div>

        </div>


        <!-- BOTÓN DE SALTO -->

        <div class="flex w-full flex-col items-center gap-3 rounded-3xl bg-white p-5 shadow-md">

            <p class="text-center text-lg font-bold text-gray-600">
                ¡Pulsa para que Piu Piu salte con su bicicleta!
            </p>

            <button
                onclick={saltar}
                class="w-full max-w-sm rounded-full bg-pink-500 px-8 py-4 text-2xl font-black text-white shadow-lg transition hover:scale-105 hover:bg-pink-600 active:scale-95"
            >
                🚲 ¡SALTAR!
            </button>

            <p class="text-sm font-semibold text-gray-400">
                También puedes utilizar la barra espaciadora
            </p>

        </div>

    </section>

</main>


<style>

.piupiu {
    position: relative;
    z-index: 20;

    width: 165px !important;
    height: 165px !important;
    margin-top: 320px;

    

    object-fit: contain;
    display: block;
    flex-shrink: 0;

    animation: salto 650ms ease-in-out;
    transform-origin: center bottom;
    pointer-events: none;
}
    /* ESCENARIO */
    .molang {
    position: relative;
    z-index: 10;

    width: 510px !important;
    height: 510px !important;

    margin-top:25px;
    margin-left:5px;

    object-fit: contain;
    flex-shrink: 0;

    animation: correr 450ms ease-in-out infinite alternate;
}

    .escenario {
        isolation: isolate;
    }


    /* PAISAJES */

    .paisaje {
        display: flex;
        width: 200%;
        height: 100%;
    }

    .paisaje img {
        display: block;
        flex: 0 0 50%;
        width: 50%;
        height: 100%;
        object-fit: cover;
    }


    /* VELOCIDADES DE DESPLAZAMIENTO */

    .paisaje-lento {
        animation: desplazamiento 60s linear infinite;
    }

    .paisaje-medio {
        animation: desplazamiento 35s linear infinite;
    }

    .paisaje-rapido {
        animation: desplazamiento 15s linear infinite;
    }

    @keyframes desplazamiento {
        from {
            transform: translateX(0);
        }

        to {
            transform: translateX(-50%);
        }
    }


    /* MOLANG CORRIENDO */

    .molang {
        animation: correr 450ms ease-in-out infinite alternate;
        transform-origin: center bottom;
        pointer-events: none;
    }

    @keyframes correr {
        from {
            transform: translateY(0) rotate(-2deg);
        }

        to {
            transform: translateY(-3px) rotate(2deg);
        }
    }


    /* SALTO DE PIU PIU */

    .piupiu {
        animation: salto 650ms ease-in-out;
        transform-origin: center bottom;
        pointer-events: none;
    }

    @keyframes salto {
        0% {
            transform: translateY(0);
        }

        35% {
            transform: translateY(-65px) rotate(-8deg);
        }

        65% {
            transform: translateY(-75px) rotate(5deg);
        }

        100% {
            transform: translateY(0);
        }
    }

    @media (max-width: 640px) {
        @keyframes salto {
            0% {
                transform: translateY(0);
            }

            40% {
                transform: translateY(-50px) rotate(-8deg);
            }

            100% {
                transform: translateY(0);
            }
        }
    }

</style>
