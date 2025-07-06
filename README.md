data class Producto(
    val nombre: String,
    val precio: Double,
    val imagen: String
)

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
