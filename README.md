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

3. Completa el método before_filter:check_admin a continuación que verifica si el campo de administrador en @user es verdadero. De lo contrario, redirija a la página admin_login con un mensaje que indica acceso restringido.

    ```ruby
    class AdminController < ApplicationController
            before_filter :check_admin
            #Completa el codigo
    ```

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

 5. ¿Cuándo deberías utilizar la metaprogramación basada en eval en lugar de la metaprogramación basada en bloques?.