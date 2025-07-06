import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import retrofit2.Call
import retrofit2.Callback
import retrofit2.Response
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory
import retrofit2.http.GET

// Modelo de datos
data class Producto(
    val nombre: String,
    val precio: Double,
    val imagen: String
)

// API Retrofit
interface ApiService {
    @GET("b/MX0A")
    fun obtenerProductos(): Call<List<Producto>>
}

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val retrofit = Retrofit.Builder()
            .baseUrl("https://jsonkeeper.com/")
            .addConverterFactory(GsonConverterFactory.create())
            .build()

        val api = retrofit.create(ApiService::class.java)

        api.obtenerProductos().enqueue(object : Callback<List<Producto>> {
            override fun onResponse(call: Call<List<Producto>>, response: Response<List<Producto>>) {
                if (response.isSuccessful) {
                    val productos = response.body() ?: emptyList()
                    productos.forEach {
                        Log.d("API", "Producto: ${it.nombre} - Precio: $${it.precio}")
                    }
                } else {
                    Log.e("API", "Error en la respuesta: ${response.code()}")
                }
            }

            override fun onFailure(call: Call<List<Producto>>, t: Throwable) {
                Log.e("API", "Error al conectar: ${t.message}")
            }
        })
    }
}
