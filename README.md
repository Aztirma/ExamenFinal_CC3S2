# Examen Final
### Alumna: Zuñiga Chicaña Alejandra Aztirma

# Parte 1

1. Produce un conflicto de fusión (merge) en algún repositorio de tus actividades realizadas. Establece los pasos y comandos que usas para resolver un conflicto de fusión en Git. Si intentas git push y falla con un mensaje como : Non-fast-forward (error): failed to push some refs esto significa que algún archivo contiene un conflicto de fusión entre la versión de tu repositorio y la versión del repositorio origen. Para este ejercicio debes presentar el conflicto dado, los pasos y comandos para resolver el problema y las solución.


2. Digamos que nos dan el modelo de User de la siguiente manera: 

    ``` 
    class User < ActiveRecord::Base
    validates :username, :presence => true
    validate :username_format
    end
    ``` 


    1. **¿Qué pasa si tenemos @user sin nombre de usuario y llamamos a @user.valid? ¿Qué 	guardará @user.save ?**  

    Si se tiene un objeto `@user` sin un nombre de usuario y llamamos a `@user.valid?`, la validación de presencia (`validates :username, :presence => true`) fallará, ya que el nombre de usuario no está presente. Esto significa que `@user.valid?` devolverá `false`. Si intentamos guardar el objeto con `@user.save`, la operación de guardado también fallará, ya que las validaciones no se cumplen.


    2. Implementa username_format. Para los propósitos, un nombre de usuario comienza 	con una letra y tiene como máximo 10 caracteres de largo. Recuerda, las validaciones 	personalizadas agregan un mensaje a la colección de errores.

    ```ruby
    class User < ActiveRecord::Base
    validates :username, :presence => true
    validate :username_format

    private

    def username_format
        unless username.blank? || /\A[A-Za-z][A-Za-z0-9]{0,9}\z/.match?(username)
        errors.add(:username, "debe comenzar con una letra y tener como máximo 10 caracteres de largo.")
        end
    end
    end
    ```

    Creamos nuestro archivo con el codigo anterior

    ![Alt text](image-16.png)

3. Completa el método before_filter:check_admin a continuación que verifica si el campo de administrador en @user es verdadero. De lo contrario, redirija a la página admin_login con un mensaje que indica acceso restringido.

    ```ruby
    class AdminController < ApplicationController
            before_filter :check_admin
            #Completa el codigo
    ```

    Completamos el código de la siguiente manera:

    
    ```ruby
    class AdminController < ApplicationController
    before_filter :check_admin

    private

    def check_admin
        unless @user.admin?
        redirect_to admin_login_path, alert: "Acceso restringido. Debes ser un administrador para ver esta página."
        end
    end
    end
    ```

    Asi como se observa a continuacion: 

    ![Alt text](image-17.png)

Este código asume que el modelo `User` tiene un método `admin?` que devuelve `true` si el usuario es un administrador y `false` de lo contrario.

4. A continuación, se te proporciona un formulario que simula el inicio de sesión. Comprueba si la combinación de nombre de usuario y contraseña funciona junto con la cuenta, si la hay. Para hacer eso, queremos que se realice una solicitud HTTP POST cuando se envíe este formulario. Escribe tu solución con jQuery y comenta dónde debe ubicarse la función de devolución de llamada (callback). Comprueba tus resultados.

    ```ruby
    <form method="POST" id="foo">
    <input type="text" class="user" />
    <input type="password" class="pass" />
    <input type="button" value="Log␣in" id="onSubmit" />
    </form>
    $("#onSubmit").click(function() {
    # Tu codigo
    })
    ```

    Completamos el código de la siguiente manera:

    ```html
    <form method="POST" id="foo">
    <input type="text" class="user" />
    <input type="password" class="pass" />
    <input type="button" value="Log in" id="onSubmit" />
    </form>

    <script>
    $("#onSubmit").click(function() {
        var username = $(".user").val();
        var password = $(".pass").val();

        $.post("/login", { username: username, password: password }, function(response) {
        console.log(response);
        });
    });
    </script>
    ```

 5. ¿Cuándo deberías utilizar la metaprogramación basada en eval en lugar de la metaprogramación basada en bloques?.

    * La metaprogramación basada en `eval` debería evitarse siempre que sea posible debido a sus riesgos de seguridad.  
    * La metaprogramación basada en bloques, generalmente es preferible ya que es más segura y menos propensa a errores. `eval` ejecuta código arbitrario, lo que puede llevar a problemas de seguridad si el código proviene de fuentes no confiables.  
    
    Por lo tanto, es aconsejable utilizar la metaprogramación basada en bloques siempre que sea posible, ya que es más segura y fácil de entender. `eval` generalmente se utiliza en casos muy específicos en los que es necesario, pero se debe usar con precaución.

# Parte 2

Para esta actividad se uso el siguiente repositorio, el cual fue proporcionado para realizar el examen final del curso, se creo otro repositorio para una mejor manejabilidad

https://github.com/Aztirma/Actividad-EF.git

Usaremos TDD para crear un controlador, que recibe la solicitud del usuario, y un modelo que en realidad llama al servicio TMDb remoto para obtener información sobre la película especificada.

Ejecutamos `bundle install` para configurar todas las dependecnias, además editamos el gemfile para poder usar algunas gemas extras ya que trabajaremos con TMDb Api y Guard.

![Alt text](image.png)

```
gem 'faraday'  
group :test do
  gem 'rails-controller-testing'
  gem 'guard-rspec'                 
end
```

![Alt text](image-1.png)

Volvemos a ejecutar bundle install para instalar las ultimas gemas que hemos añadido a nuestro archivo gemfile

![Alt text](image-2.png)

Luego ejecutamos Rails generate rspec:install para asegurarnos de que los archivos que RSpec que necesitamos estén en su lugar. 

![Alt text](image-3.png)


Editamos el archivo `spec/rails_helper.rb` para incluir require 'byebug' en la parte superior, de modo que puedas acceder al depurador según sea necesario para que las pruebas funcionen.

![Alt text](image-4.png)


Ejecutamos el paquete `exec guard init rspec` para configurar los archivos necesarios para Guard, lo que dará como resultado la creación de un nuevo Guardfile. Agrega ese archivo a tu repositorio.

![Alt text](image-5.png)


Configura la base de datos con el comando habitual `rake db:migrate`

![Alt text](image-6.png)


Ejecuta el servidor para mostrar que todo este bien.

![Alt text](image-8.png)
Al cargar la pagina observamos que no se observa ninguna pelicula, esto es por que nuestra base de datos esta vacia, se comprueba de la misma manera el la consola de rails, para ello intentamos ejecutar Movie.first para verificar si hay películas en la base de datos. Como aún no hemos agregado ninguna película, esto debería devolver "nil".

![Alt text](image-7.png)

Para agregar datos iniciales a la base de datos, copiamos el código que nos proporciona la actividad en el archivo db/seeds.rb. Este código agrega películas a la base de datos utilizando el modelo "Movie".

![Alt text](image-9.png)

No se pudo crear la base de datos debido a un problema con BigDecimal, pensé que era un problema con las versiones, pero decidi dejar por un momento de lado ese problema y continuar, al realizar los siguientes pasos sobre la vista, al cargar la pagina, ahora si se visualizaba la base de datos, cabe aclarar que no se hizo ninguna corrección, tan solo cargo la base de datos.

![Alt text](image-12.png)

Ahora ejecutamos el comando `rails generate controller search_tmdb`el cual nos crerará el controlador `search_tmdb` , el cual usaremos en los pasos posteriores 

![Alt text](image-19.png)


![Alt text](image-20.png)

## Paso 1: Escribiendo una nueva vista

1.  Llamaremos a la acción del controlador search_tmdb, Lo primero que haremos será crear la vista  rrespondiente a esa acción 

    **Vista `search_tmdb.html.erb` en la carpeta `app/views/movies`:**

    Nos aseguramos que nuestra vista `search_tmdb.html.erb` tenga un formulario con los elementos necesarios. Aquí hay un ejemplo:

    ```html
    <!-- app/views/movies/search_tmdb.html.erb -->

    <%= form_tag(search_tmdb_path, method: :get) do %>
        <%= label_tag :tmdb_form, 'Search TMDb:' %>
        <%= text_field_tag :tmdb_form, params[:tmdb_form] %>
        <%= submit_tag 'Search' %>
    <% end %>

    <%= button_to 'Back to Search', search_tmdb_path, method: :get %>
    <%= button_to 'Back to Home', root_path, method: :get %>
    ```


    Copiamos este codigo en nuestro archivo ya mencionado, así como se observa a continuación, se realizaron unos cambios de lo anterior proporcionado

    ![Alt text](image-23.png)

     Una vez completado el formulario, accedemos a la rta search_tmdb para ver si nuestras configuraciones fueron correctas o no


    ![Alt text](image-21.png)

    
    **Controlador `MoviesController` con el método `search_tmdb`:**  
    Ahora, en dicho controlador, debemos tener el método `search_tmdb` que manejará esta solicitud. Se agregara lo siguiente en `app/controllers/movies_controller.rb`:

    

    ```ruby
    # app/controllers/movies_controller.rb
    class MoviesController < ApplicationController
    def search_tmdb
        @search_term = params[:search_term]

        if @search_term.blank?
        flash[:alert] = 'Please enter a search term'
        redirect_to root_path
        else
        @movies = Tmdb::Movie.search(@search_term)
        end
    end
    end

    ```
 
    ![Alt text](image-15.png)

    **RSpec Test para el método `search_tmdb` en `movies_controller_spec.rb`:**  
    Ahora, podemos completar nuestro archivo de especificaciones con el código de prueba. A continuación se tiene una sugerencia:

    ```ruby
    # spec/controllers/movies_controller_spec.rb

    require 'rails_helper'

    describe MoviesController do
    describe 'searching TMDb' do
        it 'calls Tmdb::Movie.search with the provided search term' do
        allow(Tmdb::Movie).to receive(:search)
        
        get :search_tmdb, params: { search_term: 'Inception' }
        
        expect(Tmdb::Movie).to have_received(:search).with('Inception')
        end

        it 'renders the search_tmdb template' do
        get :search_tmdb, params: { search_term: 'Inception' }
        
        expect(response).to render_template('search_tmdb')
        end

        it 'assigns the TMDb search results to @movies' do
        movie_results = [{'title' => 'Inception', 'id' => 123}]
        allow(Tmdb::Movie).to receive(:search).with('Inception').and_return(movie_results)
        
        get :search_tmdb, params: { search_term: 'Inception' }
        
        expect(assigns(:movies)).to eq(movie_results)
        end

        it 'redirects to root_path if search term is blank' do
        get :search_tmdb, params: { search_term: '' }
        
        expect(flash[:alert]).to eq('Please enter a search term')
        expect(response).to redirect_to(root_path)
        end
    end
end
    ```

Como se observa a continuación en el archivo spec creado, se copio el codio anterior, ahora procederemos a probarlo:

![Alt text](image-13.png)

Recuerda ejecutar tus pruebas con `bundle exec rspec` para verificar su validez y avanzar en el proceso de desarrollo. También, ten en cuenta que este código es un punto d

![Alt text](image-14.png)

Como se observa no detecta ninguna prueba, puesto que no se implemento el método search_tmdb



---
## Paso 2
Vamos a revisar y completar el código siguiendo las instrucciones proporcionadas:

1. **Completa la acción `search_tmdb` en `MoviesController`:**
   Aseuremonos de que la acción del controlador `search_tmdb` llame al método del modelo `find_in_tmdb`. Aquí está el código completo:

   ```ruby
   # app/controllers/movies_controller.rb

   class MoviesController < ApplicationController
     def search_tmdb
       # Llama al método del modelo para realizar la búsqueda en TMDb
       @movies = Movie.find_in_tmdb(params[:search_terms])
       
       # Selecciona la plantilla de vista correcta para renderizar
       render 'search_tmdb'
     end
   end
   ```

2. **Completa el método `find_in_tmdb` en `Movie` para pasar la prueba:**
   Agregamos un método de clase `find_in_tmdb` en el modelo `Movie`:

   ```ruby
   # app/models/movie.rb

   class Movie < ApplicationRecord
     require 'themoviedb'

     def self.find_in_tmdb(search_terms)
       Tmdb::Api.key('tu_clave_api_tmdb')
       results = Tmdb::Movie.find(search_terms)

       results.map do |movie|
         Movie.new(title: movie.title, release_date: movie.release_date)
       end
     end
   end
   ```

   Asegúremonos de instalar la gema `themoviedb` y configurar tu clave API de TMDb en `config/initializers/tmdb.rb`.

3. **Especifica la expectativa en `movies_controller_spec.rb`:**
   Modifica la especificación en `movies_controller_spec.rb` para verificar que la acción del controlador `search_tmdb` llama al método del modelo `find_in_tmdb`:

   ```ruby
   # spec/controllers/movies_controller_spec.rb

   require 'rails_helper'

   describe MoviesController do
     describe 'searching TMDb' do
       it 'calls the model method that performs TMDb search' do
         fake_results = [double('movie1'), double('movie2')]
         expect(Movie).to receive(:find_in_tmdb).with('hardware').
           and_return(fake_results)
         get :search_tmdb, params: { search_terms: 'hardware' }
       end

       # Resto de las especificaciones...
     end
   end
   ```

   Asegúremonos de tener configurada tu aplicación y claves de API correctamente para que las pruebas se ejecuten sin problemas.
 