# Gastostelefonicos
Actividad 1 , línea profundización III ingeniería de sistemas UDEC
import kotlin.math.roundToInt

data class Llamada(val tipo: TipoLlamada, val duracion: Int)

enum class TipoLlamada(val tarifa: Int) {
    LOCAL(50),
    LARGA_DISTANCIA(350),
    CELULAR(150)
}

class CabinaTelefonica(val numero: Int) {
    private val llamadas = mutableListOf<Llamada>()

    fun registrarLlamada(tipo: TipoLlamada, duracion: Int) {
        if (duracion <= 0) throw IllegalArgumentException("La duración debe ser mayor que cero")
        llamadas.add(Llamada(tipo, duracion))
    }

    fun obtenerInformacion(): String {
        return "Cabina $numero: ${numeroLlamadas()} llamadas, ${duracionTotal()} minutos, ${costoTotal()} pesos"
    }

    fun reiniciar() {
        llamadas.clear()
    }

    fun numeroLlamadas() = llamadas.size
    fun duracionTotal() = llamadas.sumOf { it.duracion }
    fun costoTotal() = llamadas.sumOf { it.tipo.tarifa * it.duracion }
}

class ControlGastosTelefonicos {
    private val cabinas = mutableListOf<CabinaTelefonica>()

    fun agregarCabina(cabina: CabinaTelefonica) {
        cabinas.add(cabina)
    }

    fun registrarLlamada(numeroCabina: Int, tipo: TipoLlamada, duracion: Int) {
        val cabina = cabinas.find { it.numero == numeroCabina }
            ?: throw IllegalArgumentException("Cabina no encontrada")
        cabina.registrarLlamada(tipo, duracion)
    }

    fun obtenerInformacionCabina(numeroCabina: Int): String {
        val cabina = cabinas.find { it.numero == numeroCabina }
            ?: throw IllegalArgumentException("Cabina no encontrada")
        return cabina.obtenerInformacion()
    }

    fun obtenerConsolidadoTotal(): String {
        val costoTotal = cabinas.sumOf { it.costoTotal() }
        val totalLlamadas = cabinas.sumOf { it.numeroLlamadas() }
        val duracionTotal = cabinas.sumOf { it.duracionTotal() }
        val costoPromedio = if (duracionTotal > 0) (costoTotal.toDouble() / duracionTotal).roundToInt() else 0
        return "Total: $costoTotal pesos, $totalLlamadas llamadas, $duracionTotal minutos, Promedio: $costoPromedio pesos/min"
    }

    fun reiniciarCabina(numeroCabina: Int) {
        val cabina = cabinas.find { it.numero == numeroCabina }
            ?: throw IllegalArgumentException("Cabina no encontrada")
        cabina.reiniciar()
    }
}

fun main() {
    val control = ControlGastosTelefonicos()
    control.agregarCabina(CabinaTelefonica(1))
    control.agregarCabina(CabinaTelefonica(2))

    try {
        control.registrarLlamada(1, TipoLlamada.LOCAL, 5)
        control.registrarLlamada(1, TipoLlamada.LARGA_DISTANCIA, 3)
        control.registrarLlamada(2, TipoLlamada.CELULAR, 2)

        println(control.obtenerInformacionCabina(1))
        println(control.obtenerInformacionCabina(2))
        println(control.obtenerConsolidadoTotal())

        control.reiniciarCabina(1)
        println(control.obtenerInformacionCabina(1))
    } catch (e: IllegalArgumentException) {
        println("Error: ${e.message}")
    }
}
