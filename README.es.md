# Sinatra

[![Build Status](https://secure.travis-ci.org/sinatra/sinatra.svg)](http://travis-ci.org/sinatra/sinatra)

*Atención: Este documento es una traducción de la versión en inglés y puede estar desactualizado.*

Sinatra es un
[DSL](https://es.wikipedia.org/wiki/Lenguaje_específico_del_dominio) para
crear aplicaciones web rápidamente en Ruby con un mínimo esfuerzo:

```ruby
# myapp.rb
require 'sinatra'

get '/' do
  'Hello world!'
end
```

Instalar la gema:

```shell
gem install sinatra
```

Y correr la aplicación con:

```shell
ruby myapp.rb
```

Ver en: [http://localhost:4567](http://localhost:4567)

Se recomienda ejecutar `gem install thin`, ya que Sinatra lo utilizará, por defecto si se encuentra disponible.

## Tabla de contenidos

* [Sinatra](#sinatra)
    * [Tabla de contenidos](#tabla-de-contenidos)
    * [Rutas](#rutas)
    * [Condiciones](#condiciones)
    * [Valores de retorno](#valores-de-retorno)
    * [Rutas personalizadas](#rutas-personalizadas)
    * [Archivos estáticos](#archivos-estáticos)
    * [Vistas / Plantillas](#vistas--plantillas)
        * [Plantillas literales](#plantillas-literales)
        * [Lenguajes de plantillas disponibles](#lenguajes-de-plantillas-disponibles)
            * [Plantillas Haml](#plantillas-haml)
            * [Plantillas Erb](#plantillas-erb)
            * [Plantillas Builder](#plantillas-builder)
            * [Plantillas Nokogiri](#plantillas-nokogiri)
            * [Plantillas Sass](#plantillas-sass)
            * [Plantillas SCSS](#plantillas-scss)
            * [Plantillas Less](#plantillas-less)
            * [Plantillas Liquid](#plantillas-liquid)
            * [Plantillas Markdown](#plantillas-markdown)
            * [Plantillas Textile](#plantillas-textile)
            * [Plantillas RDoc](#plantillas-rdoc)
            * [Plantillas AsciiDoc](#plantillas-asciidoc)
            * [Plantillas Radius](#plantillas-radius)
            * [Plantillas Markaby](#plantillas-markaby)
            * [Plantillas RABL](#plantillas-rabl)
            * [Plantillas Slim](#plantillas-slim)
            * [Plantillas Creole](#plantillas-creole)
            * [Plantillas MediaWiki](#plantillas-mediawiki)
            * [Plantillas CoffeeScript](#plantillas-coffeescript)
            * [Plantillas Stylus](#plantillas)
            * [Plantillas Yajl](#plantillas-yajl)
            * [Plantillas WLang](#plantillas-wlang)
        * [Accediendo a las variables desde las plantillas](#accediendo-a-las-variables-desde-las-plantillas)
        * [Plantillas con `yield` y capas anidadas](#plantillas-con-yield-y-capas-anidadas)
        * [Plantillas en linea](#plantillas-en-linea)
        * [Plantillas con nombres](#plantillas-con-nombres)
        * [Asociar tipos de archivos](#asociar-tipos-de-archivo)
        * [Añadir tu propio motor de plantillas](#adding-your-own-template-engine)
        * [Using Custom Logic for Template Lookup](#using-custom-logic-for-template-lookup)
    * [Filtros](#filters)
    * [Helpers](#helpers)
        * [Sesiones](#using-sessions)
          * [Sesiones secretas](#session-secret-security)
          * [Configuracion de sesiones](#session-config)
          * [Elige tu propio Middleware de sesiones](#choosing-your-own-session-middleware)
        * [Terminar](#halting)
        * [Passing](#passing)
        * [Triggering Another Route](#triggering-another-route)
        * [Setting Body, Status Code and Headers](#setting-body-status-code-and-headers)
        * [Streaming Responses](#streaming-responses)
        * [Logging](#logging)
        * [Mime Types](#mime-types)
        * [Generating URLs](#generating-urls)
        * [Browser Redirect](#browser-redirect)
        * [Cache Control](#cache-control)
        * [Sending Files](#sending-files)
        * [Accessing the Request Object](#accessing-the-request-object)
        * [Attachments](#attachments)
        * [Dealing with Date and Time](#dealing-with-date-and-time)
        * [Looking Up Template Files](#looking-up-template-files)
    * [Configuration](#configuration)
        * [Configuring attack protection](#configuring-attack-protection)
        * [Available Settings](#available-settings)
    * [Environments](#environments)
    * [Error Handling](#error-handling)
        * [Not Found](#not-found)
        * [Error](#error)
    * [Rack Middleware](#rack-middleware)
    * [Testing](#testing)
    * [Sinatra::Base - Middleware, Libraries, and Modular Apps](#sinatrabase---middleware-libraries-and-modular-apps)
        * [Modular vs. Classic Style](#modular-vs-classic-style)
        * [Serving a Modular Application](#serving-a-modular-application)
        * [Using a Classic Style Application with a config.ru](#using-a-classic-style-application-with-a-configru)
        * [When to use a config.ru?](#when-to-use-a-configru)
        * [Using Sinatra as Middleware](#using-sinatra-as-middleware)
        * [Dynamic Application Creation](#dynamic-application-creation)
    * [Scopes and Binding](#scopes-and-binding)
        * [Application/Class Scope](#applicationclass-scope)
        * [Request/Instance Scope](#requestinstance-scope)
        * [Delegation Scope](#delegation-scope)
    * [Command Line](#command-line)
        * [Multi-threading](#multi-threading)
    * [Requirement](#requirement)
    * [The Bleeding Edge](#the-bleeding-edge)
        * [With Bundler](#with-bundler)
    * [Versioning](#versioning)
    * [Further Reading](#further-reading)

# Sinatra

## Rutas

En Sinatra, una ruta es un método HTTP junto a un patrón de un URL.
Cada ruta está asociada a un bloque:

```ruby
get '/' do
  .. Mostrar algo ..
end

post '/' do
  .. Crear algo ..
end

put '/' do
  .. Reemplazar algo ..
end

patch '/' do
  .. Modificar algo ..
end

delete '/' do
  .. Destruir algo ..
end

options '/' do
  .. Describir algo ..
end

link '/' do
  .. Relacionar algo ..
end

unlink '/' do
  .. Quitar relacion de algo ..
end
```

Las rutas son comparadas en el orden en el que son definidas. La primera ruta
que coincide con la petición es escogida.

Las rutas con barras son distintas a las demás:

```ruby
get '/foo' do
  # Does not match "GET /foo/"
end
```

Los patrones de las rutas pueden incluir parámetros nombrados, accesibles a
través del hash `params`:

```ruby
get '/hello/:name' do
  # matches "GET /hello/foo" and "GET /hello/bar"
  # params['name'] is 'foo' or 'bar'
  "Hello #{params['name']}!"
end
```

También puede acceder a los parámetros nombrados usando parámetros de bloque:

```ruby
get '/hello/:name' do |n|
  # matches "GET /hello/foo" and "GET /hello/bar"
  # params['name'] is 'foo' or 'bar'
  # n stores params['name']
  "Hello #{n}!"
end
```
Los patrones de ruta también pueden incluir parámetros splat (o wildcard),
accesibles a través del arreglo `params['splat']`:

```ruby
get '/say/*/to/*' do
  # matches /say/hello/to/world
  params['splat'] # => ["hello", "world"]
end

get '/download/*.*' do
  # matches /download/path/to/file.xml
  params['splat'] # => ["path/to/file", "xml"]
end
```

O, con parámetros de bloque:

```ruby
get '/download/*.*' do |path, ext|
  [path, ext] # => ["path/to/file", "xml"]
end
```

Rutas con Expresiones Regulares:

```ruby
get /\/hello\/([\w]+)/ do
  "Hello, #{params['captures'].first}!"
end
```

O con un parámetro de bloque:

```ruby
get %r{/hello/([\w]+)} do |c|
  # Matches "GET /meta/hello/world", "GET /hello/world/1234" etc.
  "Hello, #{c}!"
end
```

Los patrones de ruta pueden contener parámetros opcionales:

```ruby
get '/posts/:format?' do
  # matches "GET /posts/" and any extension "GET /posts/json", "GET /posts/xml" etc
end
```
Las rutas también pueden hacer uso de los parámetros enviados a través del [Query String](https://es.wikipedia.org/wiki/Query_string)

```ruby
get '/posts' do
  # matches "GET /posts?title=foo&author=bar"
  title = params['title']
  author = params['author']
  # uses title and author variables; query is optional to the /posts route
end
```

A propósito, a menos que desactives la protección para el ataque *path
traversal* (ver más abajo), el path de la petición puede ser modificado
antes de que se compare con los de tus rutas.

Tu puedes personalizar las opciones utilizadas por Mustermann, a través del hash
You may customize the Mustermann options used for a given route by passing in a
`:mustermann_opts`:

```ruby
get '\A/posts\z', :mustermann_opts => { :type => :regexp, :check_anchors => false } do
  # matches /posts exactly, with explicit anchoring
  "If you match an anchored pattern clap your hands!"
end
```
¡Parece una [condición](#condiciones), pero no lo es! Estas opciones se mezclan con las opciones globales del hash `:mustermann_opts` descriptas [más abajo].(#available-settings).

## Condiciones

Las rutas pueden incluir una variedad de condiciones de selección, como por ejemplo el user agent:

```ruby
get '/foo', :agent => /Songbird (\d\.\d)[\d\/]*?/ do
  "You're using Songbird version #{params['agent'][0]}"
end

get '/foo' do
  # Matches non-songbird browsers
end
```

Otras condiciones disponibles son `host_name` y `provides`:

```ruby
get '/', :host_name => /^admin\./ do
  "Admin Area, Access denied!"
end

get '/', :provides => 'html' do
  haml :index
end

get '/', :provides => ['rss', 'atom', 'xml'] do
  builder :feed
end
```
`provides` realiza una busqueda basada en el encabezado Accept.

Puede definir sus propias condiciones fácilmente:

```ruby
set(:probability) { |value| condition { rand <= value } }

get '/win_a_car', :probability => 0.1 do
  "You won!"
end

get '/win_a_car' do
  "Sorry, you lost."
end
```
Si su condición acepta más de un argumento, puede pasarle un arreglo. Al definir la condición, se puede utilizar el operador splat en la lista de parámetros:

```ruby
set(:auth) do |*roles|   # <- notice the splat here
  condition do
    unless logged_in? && roles.any? {|role| current_user.in_role? role }
      redirect "/login/", 303
    end
  end
end

get "/my/account/", :auth => [:user, :admin] do
  "Your Account Details"
end

get "/only/admin/", :auth => :admin do
  "Only admins are allowed here!"
end
```

## Valores de retorno

El valor de retorno de un bloque de ruta que determina al menos el cuerpo de la
respuesta que se le pasa al cliente HTTP o al siguiente middleware en la pila
de Rack. Lo más común es que sea un string, como en los ejemplos anteriores.
Sin embargo, otros valores también son aceptados.

Puede devolver cualquier objeto que sea una respuesta Rack válida, un objeto
que represente el cuerpo de una respuesta Rack o un código de estado HTTP:

* Un arreglo con tres elementos: `[estado (Fixnum), cabeceras (Hash), cuerpo de
  la respuesta (responde a #each)]`
* Un arreglo con dos elementos: `[estado (Fixnum), cuerpo de la respuesta
  (responde a #each)]`
* Un objeto que responde a `#each` y que le pasa únicamente strings al bloque
  dado
* Un Fixnum representando el código de estado

De esa manera, podemos fácilmente implementar un ejemplo de streaming:

```ruby
class Stream
  def each
    100.times { |i| yield "#{i}\n" }
  end
end

get('/') { Stream.new }
```
Se puede hacer uso del método helper `stream` (descripto más abajo) para hacer Plantillas y evitar duplicar código en las rutas.

## Rutas personalizadas

Como se mostró anteriormente, Sinatra permite utilizar strings y expresiones regulares para definir las rutas. Sin embargo, la cosa no termina ahí. Podés definir tus propios comparadores muy fácilmente:

```ruby
class AllButPattern
  Match = Struct.new(:captures)

  def initialize(except)
    @except   = except
    @captures = Match.new([])
  end

  def match(str)
    @captures unless @except === str
  end
end

def all_but(pattern)
  AllButPattern.new(pattern)
end

get all_but("/index") do
  # ...
end
```
Tenga en cuenta que el ejemplo anterior es un poco rebuscado. Un resultado similar puede conseguirse más sencillamente:

```ruby
get // do
  pass if request.path_info == "/index"
  # ...
end
```
O, usando una busqueda negativa:

```ruby
get %r{(?!/index)} do
  # ...
end
```

## Archivos Estáticos

Los archivos estáticos son servidos desde el directorio público
`./public`. Puede especificar una ubicación diferente ajustando la opción `:public_folder`:

```ruby
set :public_folder, File.dirname(__FILE__) + '/static'
```

Note que el nombre del directorio público no está incluido en la URL. Por ejemplo, el archivo `./public/css/style.css` se accede a través de
`http://ejemplo.com/css/style.css`.

Use la configuración `:static_cache_control` para agregar el encabezado
`Cache-Control` (ver la sección de configuración para más detalles).

## Vistas / Plantillas

Cada lenguaje de plantilla se expone a través de un método de renderizado que lleva su nombre. Estos métodos simplemente devuelven un string:

```ruby
get '/' do
  erb :index
end
```
Renderiza `views/index.erb`.

En lugar del nombre de la plantilla podés proporcionar directamente el
contenido de la misma:

```ruby
get '/' do
  code = "<%= Time.now %>"
  erb code
end
```

Los métodos de renderizado, aceptan además un segundo argumento, el hash de opciones:

```ruby
get '/' do
  erb :index, :layout => :post
end
```

This will render `views/index.erb` embedded in the
`views/post.erb` (default is `views/layout.erb`, if it exists).

Any options not understood by Sinatra will be passed on to the template
engine:

Renderiza `views/index.erb` incrustado en `views/post.erb` (por defecto es `views/layout.erb`, si existe).

Cualquier opción que Sinatra no entienda le será pasada al motor de renderizado de la plantilla:

```ruby
get '/' do
  haml :index, :format => :html5
end
```
Además, puede definir las opciones para un lenguaje de plantillas de forma general:

```ruby
set :haml, :format => :html5

get '/' do
  haml :index
end
```

Las opciones pasadas al método de renderizado tienen precedencia sobre las definidas mediante `set`.

Opciones disponibles:

<dl>

  <dt>locals</dt>
  <dd>
    Lista de variables locales pasadas al documento. Resultan muy útiles cuando
    se combinan con parciales.
    Ejemplo: <tt>erb "<%= foo %>", :locals => {:foo => "bar"}</tt>
  </dd>

  <dt>default_encoding</dt>
  <dd>
    Encoding utilizado cuando el de un string es dudoso. Por defecto toma el
    valor de <tt>settings.default_encoding</tt>.
  </dd>

  <dt>views</dt>
  <dd>
    Directorio desde donde se cargan las vistas. Por defecto toma el valor de
    <tt>settings.views</tt>.
  </dd>

  <dt>layout</dt>
  <dd>
    Si es <tt>true</tt> o <tt>false</tt> indica que se debe usar, o no, un layout,
    respectivamente. También puede ser un símbolo que especifique qué plantilla
    usar. Ejemplo: <tt>erb :index, :layout => !request.xhr?</tt>
  </dd>

  <dt>content_type</dt>
  <dd>
    Content-Type que produce la plantilla. El valor por defecto depende de cada
    lenguaje de plantillas.
  </dd>

  <dt>scope</dt>
  <dd>
    Ámbito en el que se renderiza la plantilla. Por defecto utiliza la instancia
    de la aplicación. Tené en cuenta que si cambiás esta opción las variables de
    instancia y los helpers van a dejar de estar disponibles.
  </dd>

  <dt>layout_engine</dt>
  <dd>
    Motor de renderizado de plantillas que usa para el layout. Resulta
    conveniente para lenguajes que no soportan layouts. Por defecto toma el valor
    del motor usado para renderizar la plantilla.
    Ejemplo: <tt>set :rdoc, :layout_engine => :erb</tt>
  </dd>

  <dd>
    Se asume que las plantillas están ubicadas directamente bajo el directorio
    <tt>./views</tt>. Para usar un directorio de vistas diferente:
    <tt>set :views, settings.root + '/plantillas'</tt>
  </dd>

  <dt>layout_options</dt>
  <dd>
    Opciones especiales que solo son usadas para renderizar el Layout. Por ejemplo <tt>set :rdoc, :layout_options => { :views => 'views/layouts' }</tt>
  </dd>
</dl>

Se asume que las plantillas se encuentran directamente en el directorio  `./views`. Para usar otro directorio:

```ruby
set :views, settings.root + '/templates'
```

Es importante acordarse que siempre tenés que referenciar a las plantillas con símbolos, incluso cuando se encuentran en un subdirectorio (en este caso tenés que usar: `:'subdir/plantilla'` o `'subdir/plantilla'.to_sym`). Tenés que usar un símbolo porque los métodos de renderización van a renderizar directamente cualquier string que se les pase como argumento.

### Plantillas literales

```ruby
get '/' do
  haml '%div.title Hello World'
end
```
Renderiza el string en formato de la plantilla. Puedes especificar el  `:path` y `:line` si tienes un path o una linea asociada con este string.

```ruby
get '/' do
  haml '%div.title Hello World', :path => 'examples/file.haml', :line => 3
end
```

### Lenguajes de Plantillas Disponibles

Algunos lenguajes tienen varias implementaciones. Para especificar que
implementación usar (y para ser thread-safe), deberías requerirla antes de
usarla:

```ruby
require 'rdiscount' # or require 'bluecloth'
get('/') { markdown :index }
```

#### Plantillas Haml

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://haml.info/" title="haml">haml</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.haml</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>haml :index, :format => :html5</tt></td>
  </tr>
</table>

#### Plantillas Erb

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      <a href="http://www.kuwata-lab.com/erubis/" title="erubis">erubis</a>
      or erb (included in Ruby)
    </td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.erb</tt>, <tt>.rhtml</tt> or <tt>.erubis</tt> (Erubis only)</td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>erb :index</tt></td>
  </tr>
</table>

#### Plantillas Builder

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      <a href="https://github.com/jimweirich/builder" title="builder">builder</a>
    </td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.builder</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>builder { |xml| xml.em "hi" }</tt></td>
  </tr>
</table>

Además, acepta un bloque con la definición de la plantilla (ver ejemplo).

#### Plantillas Nokogiri

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://www.nokogiri.org/" title="nokogiri">nokogiri</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.nokogiri</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>nokogiri { |xml| xml.em "hi" }</tt></td>
  </tr>
</table>

Además, acepta un bloque con la definición de la plantilla (ver ejemplo).

#### Plantillas Sass

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://sass-lang.com/" title="sass">sass</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.sass</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>sass :stylesheet, :style => :expanded</tt></td>
  </tr>
</table>

#### Plantillas SCSS

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://sass-lang.com/" title="sass">sass</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.scss</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>scss :stylesheet, :style => :expanded</tt></td>
  </tr>
</table>

#### Plantillas Less

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://lesscss.org/" title="less">less</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.less</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>less :stylesheet</tt></td>
  </tr>
</table>

#### Plantillas Liquid

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://liquidmarkup.org/" title="liquid">liquid</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.liquid</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>liquid :index, :locals => { :key => 'value' }</tt></td>
  </tr>
</table>

Como no va a poder llamar a métodos de Ruby (excepto por `yield`) desde una plantilla Liquid, casi siempre va a querer pasarle locales.

#### Plantillas Markdown

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      Alguna de:
        <a href="https://github.com/davidfstr/rdiscount" title="RDiscount">RDiscount</a>,
        <a href="https://github.com/vmg/redcarpet" title="RedCarpet">RedCarpet</a>,
        <a href="http://deveiate.org/projects/BlueCloth" title="BlueCloth">BlueCloth</a>,
        <a href="http://kramdown.gettalong.org/" title="kramdown">kramdown</a>,
        <a href="https://github.com/bhollis/maruku" title="maruku">maruku</a>
    </td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.markdown</tt>, <tt>.mkd</tt> and <tt>.md</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>markdown :index, :layout_engine => :erb</tt></td>
  </tr>
</table>

No es posible llamar métodos desde markdown, ni pasarle locales. Por lo tanto, generalmente va a usarlo en combinación con otro motor de renderizado:

```ruby
erb :overview, :locals => { :text => markdown(:introduction) }
```

Tenga en cuenta que también podés llamar al método `markdown` desde otras plantillas:

```ruby
%h1 Hello From Haml!
%p= markdown(:greetings)
```
Como no puede utilizar Ruby desde Markdown, no puede usar layouts escritos en
Markdown. De todos modos, es posible usar un motor de renderizado para el
layout distinto al de la plantilla pasando la opción `:layout_engine`.

#### Plantillas Textile

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://redcloth.org/" title="RedCloth">RedCloth</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.textile</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>textile :index, :layout_engine => :erb</tt></td>
  </tr>
</table>

No es posible llamar métodos desde textile, ni pasarle locales. Por lo tanto, generalmente vas a usarlo en combinación con otro motor de renderizado:

```ruby
erb :overview, :locals => { :text => textile(:introduction) }
```

Tené en cuenta que también podés llamar al método `textile` desde otras
plantillas:

```ruby
%h1 Hello From Haml!
%p= textile(:greetings)
```

Como no podés utilizar Ruby desde Textile, no podés usar layouts escritos en
Textile. De todos modos, es posible usar un motor de renderizado para el
layout distinto al de la plantilla pasando la opción `:layout_engine`.


#### Plantillas RDoc

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://rdoc.sourceforge.net/" title="RDoc">RDoc</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.rdoc</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>rdoc :README, :layout_engine => :erb</tt></td>
  </tr>
</table>

No es posible llamar métodos desde rdoc, ni pasarle locales. Por lo tanto,
generalmente vas a usarlo en combinación con otro motor de renderizado:

```ruby
erb :overview, :locals => { :text => rdoc(:introduction) }
```

Tené en cuenta que también podés llamar al método `rdoc` desde otras
plantillas:

```ruby
%h1 Hello From Haml!
%p= rdoc(:greetings)
```

Como no podés utilizar Ruby desde RDoc, no podés usar layouts escritos en RDoc.
De todos modos, es posible usar un motor de renderizado para el layout distinto
al de la plantilla pasando la opción `:layout_engine`.


#### Plantillas AsciiDoc

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://asciidoctor.org/" title="Asciidoctor">Asciidoctor</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.asciidoc</tt>, <tt>.adoc</tt> and <tt>.ad</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>asciidoc :README, :layout_engine => :erb</tt></td>
  </tr>
</table>


Desde que no se puede utilizar métodos de Ruby directamente en una
plantilla Ascii, casi siempre se necesita pasar locales.

#### Plantilla Radius

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/jlong/radius" title="Radius">Radius</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.radius</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>radius :index, :locals => { :key => 'value' }</tt></td>
  </tr>
</table>

Desde que no se puede utilizar métodos de Ruby (excepto por `yield`) de una
plantilla Radius, casi siempre se necesita pasar locales.

#### Plantillas Markaby

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://markaby.github.io/" title="Markaby">Markaby</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.mab</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>markaby { h1 "Welcome!" }</tt></td>
  </tr>
</table>

Además, acepta un bloque con la definición de la plantilla (ver ejemplo).

#### Plantillas RABL

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/nesquena/rabl" title="Rabl">Rabl</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.rabl</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>rabl :index</tt></td>
  </tr>
</table>

#### Plantillas Slim

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://slim-lang.com/" title="Slim Lang">Slim Lang</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.slim</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>slim :index</tt></td>
  </tr>
</table>

#### Plantillas Creole

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/minad/creole" title="Creole">Creole</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.creole</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>creole :wiki, :layout_engine => :erb</tt></td>
  </tr>
</table>

No es posible llamar métodos desde creole, ni pasarle locales. Por lo tanto,
generalmente va a usarlo en combinación con otro motor de renderizado:

```ruby
erb :overview, :locals => { :text => creole(:introduction) }
```

Debe tomar en cuenta que también puede llamar al método `creole` desde otras
plantillas:

```ruby
%h1 Hello From Haml!
%p= creole(:greetings)
```

Como no podés utilizar Ruby desde Creole, no podés usar layouts escritos en
Creole. De todos modos, es posible usar un motor de renderizado para el layout
distinto al de la plantilla pasando la opción `:layout_engine`.

#### Plantillas MediaWiki

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/nricciar/wikicloth" title="WikiCloth">WikiCloth</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.mediawiki</tt> and <tt>.mw</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>mediawiki :wiki, :layout_engine => :erb</tt></td>
  </tr>
</table>

No es posible llamar a métodos ruby desde MediaWiki Markup, ni tampoco pasar variables locales en ellos, Normalmente vas a usarlo en combinacion con otro motor de renderizado.

```ruby
erb :overview, :locals => { :text => mediawiki(:introduction) }
```
Note que puede tambien llamar al método `mediawiki` desde otros templates

```ruby
%h1 Hello From Haml!
%p= mediawiki(:greetings)
```

#### Plantillas CoffeeScript

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      <a href="https://github.com/josh/ruby-coffee-script" title="Ruby CoffeeScript">
        CoffeeScript
      </a> and a
      <a href="https://github.com/sstephenson/execjs/blob/master/README.md#readme" title="ExecJS">
        way to execute javascript
      </a>
    </td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.coffee</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>coffee :index</tt></td>
  </tr>
</table>

#### Plantillas Stylus

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      <a href="https://github.com/forgecrafted/ruby-stylus" title="Ruby Stylus">
        Stylus
      </a> and a
      <a href="https://github.com/sstephenson/execjs/blob/master/README.md#readme" title="ExecJS">
        way to execute javascript
      </a>
    </td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.styl</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>stylus :index</tt></td>
  </tr>
</table>

Antes de poder usar plantillas Stylus, debe cargar previamente `stylus` y  `stylus/tilt`:

```ruby
require 'sinatra'
require 'stylus'
require 'stylus/tilt'

get '/' do
  stylus :example
end
```

#### Yajl Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/brianmario/yajl-ruby" title="yajl-ruby">yajl-ruby</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.yajl</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td>
      <tt>
        yajl :index,
             :locals => { :key => 'qux' },
             :callback => 'present',
             :variable => 'resource'
      </tt>
    </td>
  </tr>
</table>

El contenido de la plantilla se evalúa como código Ruby, y la variable `json` es convertida a JSON mediante `#to_json`.

```ruby
json = { :foo => 'bar' }
json[:baz] = key
```

Las opciones `:callback` y `:variable` se pueden utilizar para decorar el objeto renderizado:


```javascript
var resource = {"foo":"bar","baz":"qux"};
present(resource);
```

#### Plantillas WLang

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/blambeau/wlang/" title="WLang">WLang</a></td>
  </tr>
  <tr>
    <td>Extensiones de Archivo</td>
    <td><tt>.wlang</tt></td>
  </tr>
  <tr>
    <td>Ejemplo</td>
    <td><tt>wlang :index, :locals => { :key => 'value' }</tt></td>
  </tr>
</table>

Como no vas a poder llamar a métodos de Ruby (excepto por `yield`) desde una
plantilla WLang, casi siempre vas a querer pasarle locales.

### Accediendo a las variables desde las plantillas

Las plantillas son evaluadas dentro del mismo contexto que los manejadores de
ruta. Las variables de instancia asignadas en los manejadores de ruta son
accesibles directamente por las plantillas:

```ruby
get '/:id' do
  @foo = Foo.find(params['id'])
  haml '%h1= @foo.name'
end
```

O es posible especificar un Hash de variables locales explícitamente:

```ruby
get '/:id' do
  foo = Foo.find(params['id'])
  haml '%h1= bar.name', :locals => { :bar => foo }
end
```

Esto es usado típicamente cuando se renderizan plantillas como parciales desde
adentro de otras plantillas.

### Plantillas con `yield` y capas anidadas
Una capa no es más que una plantilla que invoca a `yield`.
Como cualquier plantilla puede ser usada a través de la opción `:template` como
fue descripta arriba, o puede ser rendereada como un bloque, de la siguiente forma:

```ruby
erb :post, :layout => false do
  erb :index
end
```
Este código es equivalente a `erb :index, :layout => :post`.

Pasar un bloque a los métodos de rendereo es mas usado cuando se crean capas anidadas:

```ruby
erb :main_layout, :layout => false do
  erb :admin_layout do
    erb :user
  end
end
```
Esto se puede realizar en menos lineas con:

```ruby
erb :admin_layout, :layout => :main_layout do
  erb :user
end
```
Actualmente, el método de render acepta un bloque  `erb`, `haml`,
`liquid`, `slim `, `wlang`. Además el método general de `render` acepta el envio de un bloque.

### Plantillas en linea

Las plantillas pueden ser definidas al final del archivo fuente:

```ruby
require 'sinatra'

get '/' do
  haml :index
end

__END__

@@ layout
%html
  = yield

@@ index
%div.title Hello world.
```

NOTA: únicamente las plantillas inline definidas en el archivo fuente que
requiere Sinatra son cargadas automáticamente. Llamá `enable
:inline_templates` explícitamente si tenés plantillas inline en otros
archivos fuente.

### Named Templates

Las plantillas también pueden ser definidas usando el método top-level
`template`:

```ruby
template :layout do
  "%html\n  =yield\n"
end

template :index do
  '%div.title Hello World!'
end

get '/' do
  haml :index
end
```

Si existe una plantilla con el nombre "layout", va a ser usada cada vez que
una plantilla es renderizada. Podés desactivar los layouts individualmente
pasando `:layout => false` o globalmente con
`set :haml, :layout => false`:

```ruby
get '/' do
  haml :index, :layout => !request.xhr?
end
```

### Asociando Extensiones de Archivo

Para asociar una extensión de archivo con un motor de renderizado, usá
`Tilt.register`. Por ejemplo, si querés usar la extensión `tt` para
las plantillas Textile, podés hacer lo siguiente:
```ruby
Tilt.register :tt, Tilt[:textile]
```

### Agregando Tu Propio Motor de Renderizado

Primero, registrá tu motor con Tilt, y después, creá tu método de renderizado:

```ruby
Tilt.register :mipg, MiMotorParaPlantillaGenial

helpers do
  def mypg(*args) render(:mypg, *args) end
end

get '/' do
  mypg :index
end
```

Renderiza `./views/index.mypg`. Mirá https://github.com/rtomayko/tilt
para aprender más de Tilt.

### Using Custom Logic for Template Lookup

Para implementar tu mecanismo de templates, puedes escribir tu propio método `#find_template`:

```ruby
configure do
  set :views [ './views/a', './views/b' ]
end

def find_template(views, name, engine, &block)
  Array(views).each do |v|
    super(v, name, engine, &block)
  end
end
```

## Filtros

Los filtros `before` son evaluados antes de cada petición dentro del mismo
contexto que las rutas. Pueden modificar la petición y la respuesta. Las
variables de instancia asignadas en los filtros son accesibles por las rutas y
las plantillas:

```ruby
before do
  @note = 'Hi!'
  request.path_info = '/foo/bar/baz'
end

get '/foo/*' do
  @note #=> 'Hi!'
  params['splat'] #=> 'bar/baz'
end
```

Los filtros `after` son evaluados después de cada petición dentro del mismo
contexto y también pueden modificar la petición y la respuesta. Las variables
de instancia asignadas en los filtros `before` y en las rutas son accesibles por
los filtros `after`:

```ruby
after do
  puts response.status
end
```

Nota: A menos que uses el método `body` en lugar de simplemente devolver un
string desde una ruta, el cuerpo de la respuesta no va a estar disponible en
un filtro after, debido a que todavía no se ha generado.

Los filtros aceptan un patrón opcional, que cuando está presente causa que los
mismos sean evaluados únicamente si el path de la petición coincide con ese
patrón:

```ruby
before '/protected/*' do
  authenticate!
end

after '/create/:slug' do |slug|
  session[:last_slug] = slug
end
```

Al igual que las rutas, los filtros también pueden aceptar condiciones:

```ruby
before :agent => /Songbird/ do
  # ...
end

after '/blog/*', :host_name => 'example.com' do
  # ...
end
```

## Ayudantes

Usá el método top-level `helpers` para definir métodos ayudantes que
pueden ser utilizados dentro de los manejadores de rutas y las plantillas:

```ruby
helpers do
  def bar(name)
    "#{name}bar"
  end
end

get '/:name' do
  bar(params['name'])
end
```

Por cuestiones de organización, puede resultar conveniente organizar los métodos
ayudantes en distintos módulos:

```ruby
module FooUtils
  def foo(name) "#{name}foo" end
end

module BarUtils
  def bar(name) "#{name}bar" end
end

helpers FooUtils, BarUtils
```
El efecto de utilizar *helpers* de esta manera es el mismo que resulta de
incluir los módulos en la clase de la aplicación.

### Usando Sesiones

Una sesión es usada para mantener el estado a través de distintas peticiones.
Cuando están activadas, proporciona un hash de sesión para cada sesión de usuario:

```ruby
enable :sessions

get '/' do
  "value = " << session[:value].inspect
end

get '/:value' do
  session['value'] = params['value']
end
```

#### Seguridad secreta de sesión

Para incrementar la seguridad, los datos de la sesión almacenados en
la cookie son firmados con un secreto de sesión. Este secreto, es
generado aleatoriamente por Sinatra. Este secreto de sesión debería ser un
Valor aleatorio criptográficamente seguro de una longitud apropiada para
`HMAC-SHA1`, debe ser mayor o igual a 64 bytes (512 bits). Se aconseja no usar un secreto que sea menos de 32
Bytes (256 bits, 64 hex characters). Es **muy
importante** usar un generador de numeros aleatorios para crearlo.

Por defecto, sinatra va a crear un numero secreto de session de 32 bytes por ti y va a cambiar por cada reinicio de la aplicacion. Si tu tienes multiples instancias de tu aplicacion, y dejas que Sinatra genere las llaves, cada instancia crearia entonces una llave diferente de sesion, lo cual no sea lo que tu desees.

Para mas información sobre seguridad y usabilidad [recomendamos](https://12factor.net/config) que tu generes un número seguro y lo guardes como variable de entorno en cada uno de los host que estén corriendo tu aplicación, de esta forma todas las instancias compartirán el mismo secreto. Debrías encargarte de cambiar este valos periodicamente. Aquí encontrarás algunos ejemplos de como podrias generar un secreto de 64 bytes y setearlo.

**Generador de sesiones secretas**

```text
$ ruby -e "require 'securerandom'; puts SecureRandom.hex(64)"
99ae8af...snip...ec0f262ac
```

**Generador de sesiones secretas (Bonus Points)**

El uso de la gema [sysrandom](https://github.com/cryptosphere/sysrandom) es preferible al generador de `OpenSSL` que viene incluido en MRI:

```text
$ gem install sysrandom
Building native extensions.  This could take a while...
Successfully installed sysrandom-1.x
1 gem installed

$ ruby -e "require 'sysrandom/securerandom'; puts SecureRandom.hex(64)"
99ae8af...snip...ec0f262ac
```

**Secreto de sesion guardados en variables de entorno**

Se debe poder alojar en una variable de ambiente y asegurarse que pueda ser recuperada luego de un reinicio del sistema, a modo ilustrativo podria hacerse de la siguiente manera:

```bash
# echo "export SESSION_SECRET=99ae8af...snip...ec0f262ac" >> ~/.bashrc
```

**Configurar el secreto en la aplicación**

Para configurar la aplicacion de forma tolerante a fallos, si es que no existe la variable `SESSION_SECRET`

```ruby
require 'securerandom'
# -or- require 'sysrandom/securerandom'
set :session_secret, ENV.fetch('SESSION_SECRET') { SecureRandom.hex(64) }
```

#### Session Config

Si necesitas una configuración más específica, `sessions` acepta un
Hash con opciones:

```ruby
set :sessions, :domain => 'foo.com'
```
Para compartir una sesion a travez de distintas aplicaciones en distintos subdominios (en este caso .foo.com) puedes hacer lo siguiente:

```ruby
set :sessions, :domain => '.foo.com'
```

#### Eligiendo tu propio Middleware de sesiones
Tenga en cuenta que las sesiones `enable :sessions`, guarda toda la información en una cookie de sesion, pero esto podría no ser lo que usted esta buscando (por ejemplo, si guarda mucha información podría incrementar significativamente su tráfico). Si desea cambiar el sistema de sesiones, puede elegir cualquier Middleware de sesion de Rack si lo desea:

```ruby
enable :sessions
set :session_store, Rack::Session::Pool
```
O puede hacerlo a través de los hashes de configuracion:

```ruby
set :sessions, :expire_after => 2592000
set :session_store, Rack::Session::Pool
```

Otra posibilidad es la de **no** usar `enable :sessions`, y de esta forma delegar todo el manejo al middleware.
Es importante saber que cuando usa este método, el método de protección de sesiones **no va a estar habilitado por defecto**

```ruby
use Rack::Session::Pool, :expire_after => 2592000
use Rack::Protection::RemoteToken
use Rack::Protection::SessionHijacking
```
Para mas información revise la seccion de 'configurando proteccion contra ataques'

### Interrupción

Para detener inmediatamente una petición dentro de un filtro o una ruta usá:

```ruby
halt
```

También podés especificar el estado:

```ruby
halt 410
```

O el cuerpo:

```ruby
halt 'esto va a ser el cuerpo'
```

O los dos:

```ruby
halt 401, 'salí de acá!'
```

Con cabeceras:

```ruby
halt 402, { 'Content-Type' => 'text/plain' }, 'venganza'
```

Obviamente, es posible utilizar `halt` con una plantilla:

```ruby
halt erb(:error)
```

### Pasaje
Puedes hacer que sinatra corte el flujo y ejecute la próxima ruta que matchee usando `pass`:
A route can punt processing to the next matching route using `pass`:

```ruby
get '/adivinanza/:quien' do
  pass unless params['quien'] == 'Frank'
  'Lo tienes!'
end

get '/adivinanza/*' do
  'ups, vuelve a probar!'
end
```
Esto hace que la ejecución se corte y la ruta se bloquee, luego la próxima ruta que matchee con el patrón será ejecutada, en caso de no haber una se retornará 404.

### Disparando otra ruta

Puede que `pass` no sea lo que andas buscando y seas tú quien quiera escoger
la próxima ruta. Para ello puedes usar `call`:

```ruby
get '/foo' do
  status, headers, body = call env.merge("PATH_INFO" => '/bar')
  [status, headers, body.map(&:upcase)]
end

get '/bar' do
  "bar"
end
```
Debe tener en cuenta que en el ejemplo de arriba, usted podría mejorar
la performance simplemente moviendo `"bar"` dentro de un helper y llamarlo tanto
en `/foo` como en `/bar`.

Si tu quieres que el request sea procesado por la misma instancia de la aplicación en lugar de duplicarla, puede usar `call!` en lugar de `call`.

Puede checkear la especificación de Rack para más información sobre `call`.


### Asignando el Código de Estado, los Encabezados y el Cuerpo de una Respuesta

Es posible, y se recomienda, asignar el código de estado y el cuerpo de una
respuesta con el valor de retorno de una ruta. De cualquier manera, en varios
escenarios, puede que sea conveniente asignar el cuerpo en un punto arbitrario
del flujo de ejecución con el método `body`. A partir de ahí, podés usar ese
mismo método para acceder al cuerpo de la respuesta:

```ruby
get '/foo' do
  body "bar"
end

after do
  puts body
end
```

También es posible pasarle un bloque a `body`, que será ejecutado por el Rack
handler (podés usar esto para implementar streaming, mirá "Valores de retorno").

De manera similar, también podés asignar el código de estado y encabezados:

```ruby
get '/foo' do
  status 418
  headers \
    "Allow"   => "BREW, POST, GET, PROPFIND, WHEN",
    "Refresh" => "Refresh: 20; http://www.ietf.org/rfc/rfc2324.txt"
  body "I'm a tea pot!"
end
```

También, al igual que `body`, tanto `status` como `headers` pueden utilizarse
para obtener sus valores cuando no se les pasa argumentos.


### Streaming De Respuestas

A veces vas a querer empezar a enviar la respuesta a pesar de que todavía no
terminaste de generar su cuerpo. También es posible que, en algunos casos,
quieras seguir enviando información hasta que el cliente cierre la conexión.
Cuando esto ocurra, el helper `stream` te va a ser de gran ayuda:

```ruby
get '/' do
  stream do |out|
    out << "Esto va a ser legen -\n"
    sleep 0.5
    out << " (esperalo) \n"
    sleep 1
    out << "- dario!\n"
  end
end
```

Podés implementar APIs de streaming,
[Server-Sent Events](https://w3c.github.io/eventsource/) y puede ser usado
como base para [WebSockets](https://es.wikipedia.org/wiki/WebSockets). También
puede ser usado para incrementar el throughput si solo una parte del contenido
depende de un recurso lento.

Hay que tener en cuenta que el comportamiento del streaming, especialmente el
número de peticiones concurrentes, depende del servidor web utilizado para
alojar la aplicación. Puede que algunos servidores no soporten streaming
directamente, así el cuerpo de la respuesta será enviado completamente de una
vez cuando el bloque pasado a `stream` finalice su ejecución. Si estás usando
Shotgun, el streaming no va a funcionar.

Cuando se pasa `keep_open` como parámetro, no se va a enviar el mensaje
`close` al objeto de stream. Queda en vos cerrarlo en el punto de ejecución
que quieras. Nuevamente, hay que tener en cuenta que este comportamiento es
posible solo en servidores que soporten eventos, como Thin o Rainbows. El
resto de los servidores van a cerrar el stream de todos modos:

```ruby
set :server, :thin
conexiones = []

get '/' do
  # mantenemos abierto el stream
  stream(:keep_open) { |salida| conexiones << salida }
end

post '/' do
  # escribimos a todos los streams abiertos
  conexiones.each { |salida| salida << params['mensaje'] << "\n" }
  "mensaje enviado"
end
```
### Log (Registro)

En el ámbito de la petición, el helper `logger` (registrador) expone
una instancia de `Logger`:

```ruby
get '/' do
  logger.info "cargando datos"
  # ...
end
```

Este logger tiene en cuenta la configuración de logueo de tu Rack
handler. Si el logueo está desactivado, este método va a devolver un
objeto que se comporta como un logger pero que en realidad no hace
nada. Así, no vas a tener que preocuparte por esta situación.

Tené en cuenta que el logueo está habilitado por defecto únicamente
para `Sinatra::Application`. Si heredaste de
`Sinatra::Base`, probablemente quieras habilitarlo manualmente:

```ruby
class MiApp < Sinatra::Base
  configure :production, :development do
    enable :logging
  end
end
```

Para evitar que se inicialice cualquier middleware de logging, configurá
`logging` a `nil`. Tené en cuenta que, cuando hagas esto, `logger` va a
devolver `nil`. Un caso común es cuando querés usar tu propio logger. Sinatra
va a usar lo que encuentre en `env['rack.logger']`.

### Tipos Mime

Cuando usás `send_file` o archivos estáticos tal vez tengas tipos mime
que Sinatra no entiende. Usá `mime_type` para registrarlos a través de la
extensión de archivo:

```ruby
configure do
  mime_type :foo, 'text/foo'
end
```

También lo podés usar con el ayudante `content_type`:

```ruby
get '/' do
  content_type :foo
  "foo foo foo"
end
```

### Generando URLs

Para generar URLs deberías usar el método `url`. Por ejemplo, en Haml:

```ruby
%a{:href => url('/foo')} foo
```

Tiene en cuenta proxies inversos y encaminadores de Rack, si están presentes.

Este método también puede invocarse mediante su alias `to` (mirá un ejemplo
a continuación).

### Redirección del Navegador

Podés redireccionar al navegador con el método `redirect`:

```ruby
get '/foo' do
  redirect to('/bar')
end
```

Cualquier parámetro adicional se utiliza de la misma manera que los argumentos
pasados a `halt`:

```ruby
redirect to('/bar'), 303
redirect 'http://www.google.com/', 'te confundiste de lugar, compañero'
```

También podés redireccionar fácilmente de vuelta hacia la página desde donde
vino el usuario con `redirect back`:

```ruby
get '/foo' do
  "<a href='/bar'>hacer algo</a>"
end

get '/bar' do
  hacer_algo
  redirect back
end
```

Para pasar argumentos con una redirección, podés agregarlos a la cadena de
búsqueda:

```ruby
redirect to('/bar?suma=42')
```

O usar una sesión:

```ruby
enable :sessions

get '/foo' do
  session[:secreto] = 'foo'
  redirect to('/bar')
end

get '/bar' do
  session[:secreto]
end
```

### Control de cache
Asignar tus encabezados correctamente es el cimiento para realizar un cacheo
HTTP correcto.

Podés asignar el encabezado Cache-Control fácilmente:

```ruby
get '/' do
  cache_control :public
  "cachealo!"
end
```

Pro tip: configurar el cacheo en un filtro `before`:

```ruby
before do
  cache_control :public, :must_revalidate, :max_age => 60
end
```

Si estás usando el helper `expires` para definir el encabezado correspondiente,
`Cache-Control` se va a definir automáticamente:

```ruby
before do
  expires 500, :public, :must_revalidate
end
```

Para usar cachés adecuadamente, deberías considerar usar `etag` o
`last_modified`. Es recomendable que llames a estos asistentes *antes* de hacer
cualquier trabajo pesado, ya que van a enviar la respuesta inmediatamente si
el cliente ya tiene la versión actual en su caché:

```ruby
get '/articulo/:id' do
  @articulo = Articulo.find params['id']
  last_modified @articulo.updated_at
  etag @articulo.sha1
  erb :articulo
end
```

También es posible usar una
[weak ETag](https://en.wikipedia.org/wiki/HTTP_ETag#Strong_and_weak_validation):

```ruby
etag @articulo.sha1, :weak
```

Estos helpers no van a cachear nada por vos, sino que van a facilitar la
información necesaria para poder hacerlo. Si estás buscando soluciones rápidas
de cacheo con proxys reversos, mirá
[rack-cache](https://github.com/rtomayko/rack-cache):

```ruby
require "rack/cache"
require "sinatra"

use Rack::Cache

get '/' do
  cache_control :public, :max_age => 36000
  sleep 5
  "hola"
end
```

Usá la configuración `:static_cache_control` para agregar el encabezado
`Cache-Control` a archivos estáticos (ver la sección de configuración
para más detalles).

De acuerdo con la RFC 2616 tu aplicación debería comportarse diferente si a las
cabeceras If-Match o If-None-Match se le asigna el valor `*` cuando el
recurso solicitado ya existe. Sinatra asume para peticiones seguras (como get)
y potentes (como put) que el recurso existe, mientras que para el resto
(como post) asume que no. Podés cambiar este comportamiento con la opción
`:new_resource`:

```ruby
get '/crear' do
  etag '', :new_resource => true
  Articulo.create
  erb :nuevo_articulo
end
```

Si querés seguir usando una weak ETag, indicalo con la opción `:kind`:

```ruby
etag '', :new_resource => true, :kind => :weak
```

### Enviando Archivos

Para enviar archivos, podés usar el método `send_file`:

```ruby
get '/' do
  send_file 'foo.png'
end
```

Además acepta un par de opciones:

```ruby
send_file 'foo.png', :type => :jpg
```

Estas opciones son:

[filename]
  nombre del archivo devuelto, por defecto es el nombre real del archivo.

[last_modified]
  valor para el encabezado Last-Modified, por defecto toma el mtime del archivo.

[type]
  el content type que se va a utilizar, si no está presente se intenta adivinar
  a partir de la extensión del archivo.

[disposition]
  se utiliza para el encabezado Content-Disposition, y puede tomar alguno de los
  siguientes valores: `nil` (por defecto), `:attachment` e
  `:inline`

[length]
  encabezado Content-Length, por defecto toma el tamaño del archivo.

[status]
  código de estado devuelto. Resulta útil al enviar un archivo estático como una
  página de error.

Si el Rack handler lo soporta, se intentará no transmitir directamente desde el
proceso de Ruby. Si usás este método, Sinatra se va a encargar automáticamente de las
peticiones de rango.

### Accediendo al objeto de la petición

El objeto de la petición entrante puede ser accedido desde el nivel de la
petición (filtros, rutas y manejadores de errores) a través del método
`request`:

```ruby
# app corriendo en http://ejemplo.com/ejemplo
get '/foo' do
  t = %w[text/css text/html application/javascript]
  request.accept              # ['text/html', '*/*']
  request.accept? 'text/xml'  # true
  request.preferred_type(t)   # 'text/html'
  request.body                # cuerpo de la petición enviado por el cliente (ver más abajo)
  request.scheme              # "http"
  request.script_name         # "/ejemplo"
  request.path_info           # "/foo"
  request.port                # 80
  request.request_method      # "GET"
  request.query_string        # ""
  request.content_length      # longitud de request.body
  request.media_type          # tipo de medio de request.body
  request.host                # "ejemplo.com"
  request.get?                # true (hay métodos análogos para los otros verbos)
  request.form_data?          # false
  request["UNA_CABECERA"]     # valor de la cabecera UNA_CABECERA
  request.referrer            # la referencia del cliente o '/'
  request.user_agent          # user agent (usado por la condición :agent)
  request.cookies             # hash de las cookies del navegador
  request.xhr?                # es una petición ajax?
  request.url                 # "http://ejemplo.com/ejemplo/foo"
  request.path                # "/ejemplo/foo"
  request.ip                  # dirección IP del cliente
  request.secure?             # false (sería true sobre ssl)
  request.forwarded?          # true (si se está corriendo atrás de un proxy reverso)
  request.env                 # hash de entorno directamente entregado por Rack
end
```

Algunas opciones, como `script_name` o `path_info` pueden
también ser escritas:

```ruby
before { request.path_info = "/" }

get "/" do
  "todas las peticiones llegan acá"
end
```

El objeto `request.body` es una instancia de IO o StringIO:

```ruby
post "/api" do
  request.body.rewind  # en caso de que alguien ya lo haya leído
  datos = JSON.parse request.body.read
  "Hola #{datos['nombre']}!"
end
```

### Archivos Adjuntos

Podés usar el helper `attachment` para indicarle al navegador que
almacene la respuesta en el disco en lugar de mostrarla en pantalla:

```ruby
get '/' do
  attachment
  "guardalo!"
end
```

También podés pasarle un nombre de archivo:

```ruby
get '/' do
  attachment "info.txt"
  "guardalo!"
end
```

### Fecha y Hora

Sinatra pone a tu disposición el helper `time_for`, que genera un objeto `Time`
a partir del valor que recibe como argumento. Este valor puede ser un
`String`, pero también es capaz de convertir objetos `DateTime`, `Date` y de
otras clases similares:

```ruby
get '/' do
  pass if Time.now > time_for('Dec 23, 2012')
  "todavía hay tiempo"
end
```

Este método es usado internamente por métodos como `expires` y `last_modified`,
entre otros. Por lo tanto, es posible extender el comportamiento de estos
métodos sobreescribiendo `time_for` en tu aplicación:

```ruby
helpers do
  def time_for(value)
    case value
    when :ayer then Time.now - 24*60*60
    when :mañana then Time.now + 24*60*60
    else super
    end
  end
end

get '/' do
  last_modified :ayer
  expires :mañana
  "hola"
end
```
### Buscando los Archivos de las Plantillas

El helper `find_template` se utiliza para encontrar los archivos de las
plantillas que se van a renderizar:

```ruby
find_template settings.views, 'foo', Tilt[:haml] do |archivo|
  puts "podría ser #{archivo}"
end
```

Si bien esto no es muy útil, lo interesante es que podés sobreescribir este
método, y así enganchar tu propio mecanismo de búsqueda. Por ejemplo, para
poder utilizar más de un directorio de vistas:

```ruby
set :views, ['vistas', 'plantillas']

helpers do
  def find_template(views, name, engine, &block)
    Array(views).each { |v| super(v, name, engine, &block) }
  end
end
```

Otro ejemplo consiste en usar directorios diferentes para los distintos motores
de renderizado:

```ruby
set :views, :sass => 'vistas/sass', :haml => 'plantillas', :defecto => 'vistas'

helpers do
  def find_template(views, name, engine, &block)
    _, folder = views.detect { |k,v| engine == Tilt[k] }
    folder ||= views[:defecto]
    super(folder, name, engine, &block)
  end
end
```

¡Es muy fácil convertir estos ejemplos en una extensión y compartirla!

Notá que `find_template` no verifica si un archivo existe realmente, sino
que llama al bloque que recibe para cada path posible. Esto no representa un
problema de rendimiento debido a que `render` va a usar `break` ni bien
encuentre un archivo que exista. Además, las ubicaciones de las plantillas (y
su contenido) se cachean cuando no estás en el modo de desarrollo. Es bueno
tener en cuenta lo anterior si escribís un método extraño.

## Configuración

Ejecutar una vez, en el inicio, en cualquier entorno:

```ruby
configure do
  # asignando una opción
  set :opcion, 'valor'

  # asignando varias opciones
  set :a => 1, :b => 2

  # atajo para `set :opcion, true`
  enable :opcion

  # atajo para `set :opcion, false`
  disable :opcion

  # también podés tener configuraciones dinámicas usando bloques
  set(:css_dir) { File.join(views, 'css') }
end
```

Ejecutar únicamente cuando el entorno (la variable de entorno APP_ENV) es
`:production`:

```ruby
configure :production do
  ...
end
```

Ejecutar cuando el entorno es `:production` o `:test`:

```ruby
configure :production, :test do
  ...
end
```

Podés acceder a estas opciones utilizando el método `settings`:

```ruby
configure do
  set :foo, 'bar'
end

get '/' do
  settings.foo? # => true
  settings.foo  # => 'bar'
  ...
end
```

### Configurando la Protección de Ataques

Sinatra usa [Rack::Protection](https://github.com/sinatra/rack-protection#readme)
para defender a tu aplicación de los ataques más comunes. Si por algún motivo,
querés desactivar esta funcionalidad, podés hacerlo como se indica a
continuación (ten en cuenta que tu aplicación va a quedar expuesta a un
montón de vulnerabilidades bien conocidas):

```ruby
disable :protection
```

También es posible desactivar una única capa de defensa:

```ruby
set :protection, :except => :path_traversal
```

O varias:

```ruby
set :protection, :except => [:path_traversal, :session_hijacking]
```

### Configuraciones Disponibles

<dl>
  <dt>absolute_redirects</dt>
  <dd>
    Si está deshabilitada, Sinatra va a permitir
    redirecciones relativas, sin embargo, como consecuencia
    de esto, va a dejar de cumplir con el RFC 2616 (HTTP
    1.1), que solamente permite redirecciones absolutas.

    Activalo si tu apliación está corriendo atrás de un proxy
    reverso que no se ha configurado adecuadamente. Notá que
    el helper <tt>url</tt> va a seguir produciendo URLs absolutas, a
    menos que le pasés <tt>false</tt> como segundo parámetro.

    Deshabilitada por defecto.
  </dd>

  <dt>add_charset</dt>
  <dd>
    Tipos mime a los que el helper <tt>content_type</tt> les
    añade automáticamente el charset.

    En general, no deberías asignar directamente esta opción,
    sino añadirle los charsets que quieras:
    <tt>settings.add_charset &lt;&lt; "application/foobar"</tt>
  </dd>

  <dt>app_file</dt>
  <dd>
    Path del archivo principal de la aplicación, se utiliza
    para detectar la raíz del proyecto, el directorio de las
    vistas y el público, así como las plantillas inline.
  </dd>

  <dt>bind</dt>
  <dd>
    Dirección IP que utilizará el servidor integrado (por
    defecto: 0.0.0.0).
  </dd>

  <dt>default_encoding</dt>
  <dd>
    Encoding utilizado cuando el mismo se desconoce (por
    defecto <tt>"utf-8"</tt>).
  </dd>

  <dt>dump_errors</dt>
  <dd>
    Mostrar errores en el log.
  </dd>

  <dt>environment</dt>
  <dd>
    Entorno actual, por defecto toma el valor de
    <tt>ENV['APP_ENV']</tt>, o <tt>"development"</tt> si no
    está disponible.
  </dd>

  <dt>logging</dt>
  <dd>
    Define si se utiliza el logger.
  </dd>

  <dt>lock</dt>
  <dd>
    Coloca un lock alrededor de cada petición, procesando
    solamente una por proceso.

    Habilitá esta opción si tu aplicación no es thread-safe.
    Se encuentra deshabilitada por defecto.
  </dd>

  <dt>method_override</dt>
  <dd>
    Utiliza el parámetro <tt>_method</tt> para permtir
    formularios put/delete en navegadores que no los
    soportan.
  </dd>

  <dt>port</dt>
  <dd>
    Puerto en el que escuchará el servidor integrado.
  </dd>

  <dt>prefixed_redirects</dt>
  <dd>
    Define si inserta <tt>request.script_name</tt> en las
    redirecciones cuando no se proporciona un path absoluto.
    De esta manera, cuando está habilitada,
    <tt>redirect '/foo'</tt> se comporta de la misma manera
    que <tt>redirect to('/foo')</tt>. Se encuentra
    deshabilitada por defecto.
  </dd>

  <dt>protection</dt>
  <dd>
    Define si deben activarse las protecciones para los
    ataques web más comunes. Para más detalles mirá la
    sección sobre la configuración de protección de ataques
    más arriba.
  </dd>

  <dt>public_dir</dt>
  <dd>
    Alias para <tt>public_folder</tt>, que se encuentra a
    continuación.
  </dd>

  <dt>public_folder</dt>
  <dd>
    Lugar del directorio desde donde se sirven los archivos
    públicos. Solo se utiliza cuando se sirven archivos
    estáticos (ver la opción <tt>static</tt>). Si no
    está presente, se infiere del valor de la opción
    <tt>app_file</tt>.
  </dd>

  <dt>reload_templates</dt>
  <dd>
    Define si se recargan las plantillas entre peticiones.

    Se encuentra activado en el entorno de desarrollo.
  </dd>

  <dt>root</dt>
  <dd>
    Lugar del directorio raíz del proyecto. Si no está
    presente, se infiere del valor de la opción
    <tt>app_file</tt>.
  </dd>

  <dt>raise_errors</dt>
  <dd>
    Elevar excepciones (detiene la aplicación). Se
    encuentra activada por defecto cuando el valor de
    <tt>environment</tt>  es <tt>"test"</tt>. En caso
    contrario estará desactivada.
  </dd>

  <dt>run</dt>
  <dd>
    Cuando está habilitada, Sinatra se va a encargar de
    iniciar el servidor web, no la habilites cuando estés
    usando rackup o algún otro medio.
  </dd>

  <dt>running</dt>
  <dd>
    Indica si el servidor integrado está ejecutándose, ¡no
    cambiés esta configuración!.
  </dd>

  <dt>server</dt>
  <dd>
    Servidor, o lista de servidores, para usar como servidor
    integrado. Por defecto: <tt>['thin', 'mongrel', 'webrick']</tt>,
    el orden establece la prioridad.
  </dd>

  <dt>sessions</dt>
  <dd>
    Habilita el soporte de sesiones basadas en cookies a
    través de <tt>Rack::Session::Cookie</tt>. Ver la
    sección 'Usando Sesiones' para más información.
  </dd>

  <dt>show_exceptions</dt>
  <dd>
    Muestra un stack trace en el navegador cuando ocurre una
    excepción. Se encuentra activada por defecto cuando el
    valor de <tt>environment</tt> es <tt>"development"</tt>.
    En caso contrario estará desactivada.
  </dd>

  <dt>static</dt>
  <dd>
    Define si Sinatra debe encargarse de servir archivos
    estáticos.

    Deshabilitala cuando uses un servidor capaz de
    hacerlo por sí solo, porque mejorará el
    rendimiento. Se encuentra habilitada por
    defecto en el estilo clásico y desactivado en el
    el modular.
  </dd>

  <dt>static_cache_control</dt>
  <dd>
    Cuando Sinatra está sirviendo archivos estáticos, y
    esta opción está habilitada, les va a agregar encabezados
    <tt>Cache-Control</tt> a las respuestas. Para esto
    utiliza el helper <tt>cache_control</tt>. Se encuentra
    deshabilitada por defecto. Notar que es necesario
    utilizar un array cuando se asignan múltiples valores:
    <tt>set :static_cache_control, [:public, :max_age => 300]</tt>.
  </dd>

  <dt>views</dt>
  <dd>
    Path del directorio de las vistas. Si no está presente,
    se infiere del valor de la opción <tt>app_file</tt>.
  </dd>
</dl>

## Ambientes

Existen tres entornos (`environments`) predefinidos: `development`,
`production` y `test`, Puede ser configurado a través de la variable de entorno `APP_ENV`,  el entorno por defecto es  `"development"`, al correr en este ambiente existen algunas particularidades:
* Todos los templates serán recargados entre cada request.
* Tiene un manejo especial de errores que permitiran ver las distintas llamadas.

En los ambientes de `"production"` y `"test"` las plantillas serán cacheadas por default.

Para cambiar entre ambientes, se deberá setear la variable de entorno `APP_ENV`

```shell
APP_ENV=production ruby my_app.rb
```

Los métodos `development?`, `test?` y `production?` te permiten conocer el
entorno actual.

```ruby
get '/' do
  if settings.development?
    "development!"
  else
    "not development!"
  end
end
```
## Manejo de Errores

Los manejadores de errores se ejecutan dentro del mismo contexto que las rutas
y los filtros `before`, lo que significa que podés usar, por ejemplo,
`haml`, `erb`, `halt`, etc.

### No encontrado <em>(Not Found)</em>

Cuando se eleva una excepción `Sinatra::NotFound`, o el código de
estado de la respuesta es 404, el manejador `not_found` es invocado:

```ruby
not_found do
  'No existo'
end
```

### Error

El manejador `error` es invocado cada vez que una excepción es elevada
desde un bloque de ruta o un filtro. El objeto de la excepción se puede
obtener de la variable Rack `sinatra.error`:

```ruby
error do
  'Disculpá, ocurrió un error horrible - ' + env['sinatra.error'].message
end
```

Errores personalizados:

```ruby
error MiErrorPersonalizado do
  'Lo que pasó fue...' + env['sinatra.error'].message
end
```

Entonces, si pasa esto:

```ruby
get '/' do
  raise MiErrorPersonalizado, 'algo malo'
end
```

Obtenés esto:

  Lo que pasó fue... algo malo

También, podés instalar un manejador de errores para un código de estado:

```ruby
error 403 do
  'Acceso prohibido'
end

get '/secreto' do
  403
end
```

O un rango:

```ruby
error 400..510 do
  'Boom'
end
```

Sinatra instala manejadores `not_found` y `error` especiales
cuando se ejecuta dentro del entorno de desarrollo "development".

## Rack Middleware

Sinatra corre sobre [Rack](http://rack.github.io/), una interfaz minimalista
que es un estándar para frameworks webs escritos en Ruby. Una de las
características más interesantes de Rack para los desarrolladores de aplicaciones
es el soporte de "middleware" -- componentes que se ubican entre el servidor y
tu aplicación, supervisando y/o manipulando la petición/respuesta HTTP para
proporcionar varios tipos de funcionalidades comunes.

Sinatra hace muy sencillo construir tuberías de Rack middleware a través del
método top-level `use`:

```ruby
require 'sinatra'
require 'mi_middleware_personalizado'

use Rack::Lint
use MiMiddlewarePersonalizado

get '/hola' do
  'Hola Mundo'
end
```

La semántica de `use` es idéntica a la definida para el DSL
Rack::Builder[http://www.rubydoc.info/github/rack/rack/master/Rack/Builder] (más
frecuentemente usado en archivos rackup). Por ejemplo, el método `use`
acepta argumentos múltiples/variables así como bloques:

```ruby
use Rack::Auth::Basic do |nombre_de_usuario, password|
  nombre_de_usuario == 'admin' && password == 'secreto'
end
```

Rack es distribuido con una variedad de middleware estándar para logging,
debugging, enrutamiento URL, autenticación y manejo de sesiones. Sinatra
usa muchos de estos componentes automáticamente de acuerdo a su configuración
para que usualmente no tengas que usarlas (con `use`) explícitamente.

Podés encontrar middleware útil en
[rack](https://github.com/rack/rack/tree/master/lib/rack),
[rack-contrib](https://github.com/rack/rack-contrib#readme),
o en la [Rack wiki](https://github.com/rack/rack/wiki/List-of-Middleware).

## Pruebas

Las pruebas para las aplicaciones Sinatra pueden ser escritas utilizando
cualquier framework o librería de pruebas basada en Rack. Se recomienda usar
[Rack::Test](http://www.rubydoc.info/github/brynary/rack-test/master/frames):

```ruby
require 'mi_app_sinatra'
require 'minitest/autorun'
require 'rack/test'

class MiAppTest < Minitest::Test
  include Rack::Test::Methods

  def app
    Sinatra::Application
  end

  def test_mi_defecto
    get '/'
    assert_equal 'Hola Mundo!', last_response.body
  end

  def test_con_parametros
    get '/saludar', :name => 'Franco'
    assert_equal 'Hola Frank!', last_response.body
  end

  def test_con_entorno_rack
    get '/', {}, 'HTTP_USER_AGENT' => 'Songbird'
    assert_equal "Estás usando Songbird!", last_response.body
  end
end
```
## Sinatra::Base - Middleware, Librerías, y Aplicaciones Modulares

Definir tu aplicación en el nivel superior funciona bien para micro-aplicaciones
pero trae inconvenientes considerables a la hora de construir componentes
reutilizables como Rack middleware, Rails metal, librerías simples con un
componente de servidor o incluso extensiones de Sinatra. El DSL de alto nivel
asume una configuración apropiada para micro-aplicaciones (por ejemplo, un
único archivo de aplicación, los directorios `./public` y
`./views`, logging, página con detalles de excepción, etc.). Ahí es
donde `Sinatra::Base` entra en el juego:

```ruby
require 'sinatra/base'

class MiApp < Sinatra::Base
  set :sessions, true
  set :foo, 'bar'

  get '/' do
    'Hola Mundo!'
  end
end
```

Las subclases de `Sinatra::Base` tienen disponibles exactamente los
mismos métodos que los provistos por el DSL de top-level. La mayoría de las
aplicaciones top-level se pueden convertir en componentes
`Sinatra::Base` con dos modificaciones:

* Tu archivo debe requerir `sinatra/base` en lugar de `sinatra`; de otra
  manera, todos los métodos del DSL de sinatra son importados dentro del
  espacio de nombres principal.
* Poné las rutas, manejadores de errores, filtros y opciones de tu aplicación
  en una subclase de `Sinatra::Base`.

`Sinatra::Base` es una pizarra en blanco. La mayoría de las opciones están
desactivadas por defecto, incluyendo el servidor incorporado. Mirá
[Opciones y Configuraciones](http://www.sinatrarb.com/configuration.html)
para detalles sobre las opciones disponibles y su comportamiento.

### Estilo Modular vs. Clásico

Contrariamente a la creencia popular, no hay nada de malo con el estilo clásico.
Si se ajusta a tu aplicación, no es necesario que la cambies a una modular.

La desventaja de usar el estilo clásico en lugar del modular consiste en que
solamente podés tener una aplicación Sinatra por proceso Ruby. Si tenés
planificado usar más, cambiá al estilo modular. Al mismo tiempo, ten en
cuenta que no hay ninguna razón por la cuál no puedas mezclar los estilos
clásico y modular.

A continuación se detallan las diferencias (sútiles) entre las configuraciones
de ambos estilos:

<table>
  <tr>
    <td>Configuración</td>
    <td>Clásica</td>
    <td>Modular</td>
  </tr>

  <tr>
    <td>app_file</td>
    <td>archivo que carga sinatra</td>
    <td>archivo con la subclase de Sinatra::Base</td>
  </tr>

  <tr>
    <td>run</td>
    <td>$0 == app_file</td>
    <td>false</td>
  </tr>

  <tr>
    <td>logging</td>
    <td>true</td>
    <td>false</td>
  </tr>

  <tr>
    <td>method_override</td>
    <td>true</td>
    <td>false</td>
  </tr>

  <tr>
    <td>inline_templates</td>
    <td>true</td>
    <td>false</td>
  </tr>

  <tr>
    <td>static</td>
    <td>true</td>
    <td>File.exist?(public_folder)</td>
  </tr>
</table>

### Sirviendo una Aplicación Modular

Las dos opciones más comunes para iniciar una aplicación modular son, iniciarla
activamente con `run!`:

```ruby
# mi_app.rb
require 'sinatra/base'

class MiApp < Sinatra::Base
  # ... código de la app  ...

  # iniciar el servidor si el archivo fue ejecutado directamente
  run! if app_file == $0
end
```

Iniciar con:

```shell
ruby mi_app.rb
```

O, con un archivo `config.ru`, que permite usar cualquier handler Rack:

```ruby
# config.ru
require './mi_app'
run MiApp
```

Después ejecutar:

```shell
rackup -p 4567
```

### Usando una Aplicación Clásica con un Archivo config.ru

Escribí el archivo de tu aplicación:

```ruby
# app.rb
require 'sinatra'

get '/' do
  'Hola mundo!'
end
```

Y el `config.ru` correspondiente:

```ruby
require './app'
run Sinatra::Application
```

### ¿Cuándo usar config.ru?

Indicadores de que probablemente querés usar `config.ru`:

* Querés realizar el deploy con un handler Rack distinto (Passenger, Unicorn,
  Heroku, ...).
* Querés usar más de una subclase de `Sinatra::Base`.
* Querés usar Sinatra únicamente para middleware, pero no como un endpoint.

**No hay necesidad de utilizar un archivo `config.ru` exclusivamente
porque tenés una aplicación modular, y no necesitás una aplicación modular para
iniciarla con `config.ru`.**

### Utilizando Sinatra como Middleware

Sinatra no solo es capaz de usar otro Rack middleware, sino que a su vez,
cualquier aplicación Sinatra puede ser agregada delante de un endpoint Rack
como middleware. Este endpoint puede ser otra aplicación Sinatra, o cualquier
aplicación basada en Rack (Rails/Ramaze/Camping/...):

```ruby
require 'sinatra/base'

class PantallaDeLogin < Sinatra::Base
  enable :sessions

  get('/login') { haml :login }

  post('/login') do
    if params['nombre'] == 'admin' && params['password'] == 'admin'
      session['nombre_de_usuario'] = params['nombre']
    else
      redirect '/login'
    end
  end
end

class MiApp < Sinatra::Base
  # el middleware se ejecutará antes que los filtros
  use PantallaDeLogin

  before do
    unless session['nombre_de_usuario']
      halt "Acceso denegado, por favor <a href='/login'>iniciá sesión</a>."
    end
  end

  get('/') { "Hola #{session['nombre_de_usuario']}." }
end
```

### Creación Dinámica de Aplicaciones

Puede que en algunas ocasiones quieras crear nuevas aplicaciones en
tiempo de ejecución sin tener que asignarlas a una constante. Para
esto tenés `Sinatra.new`:

```ruby
require 'sinatra/base'
mi_app = Sinatra.new { get('/') { "hola" } }
mi_app.run!
```

Acepta como argumento opcional una aplicación desde la que se
heredará:

```ruby
# config.ru
require 'sinatra/base'

controller = Sinatra.new do
  enable :logging
  helpers MisHelpers
end

map('/a') do
  run Sinatra.new(controller) { get('/') { 'a' } }
end

map('/b') do
  run Sinatra.new(controller) { get('/') { 'b' } }
end
```

Construir aplicaciones de esta forma resulta especialmente útil para
testear extensiones Sinatra o para usar Sinatra en tus librerías.

Por otro lado, hace extremadamente sencillo usar Sinatra como
middleware:

```ruby
require 'sinatra/base'

use Sinatra do
  get('/') { ... }
end

run ProyectoRails::Application
```

### Ámbito de Aplicación/Clase

Cada aplicación Sinatra es una subclase de `Sinatra::Base`. Si estás
usando el DSL de top-level (`require 'sinatra'`), entonces esta clase es
`Sinatra::Application`, de otra manera es la subclase que creaste
explícitamente. Al nivel de la clase tenés métodos como `get` o `before`, pero
no podés acceder a los objetos `request` o `session`, ya que hay una única
clase de la aplicación para todas las peticiones.

Las opciones creadas utilizando `set` son métodos al nivel de la clase:

```ruby
class MiApp < Sinatra::Base
  # Ey, estoy en el ámbito de la aplicación!
  set :foo, 42
  foo # => 42

  get '/foo' do
    # Hey, ya no estoy en el ámbito de la aplicación!
  end
end
```

Tenés la ligadura al ámbito de la aplicación dentro de:

* El cuerpo de la clase de tu aplicación
* Métodos definidos por extensiones
* El bloque pasado a `helpers`
* Procs/bloques usados como el valor para `set`

Este ámbito puede alcanzarse de las siguientes maneras:

* A través del objeto pasado a los bloques de configuración (`configure { |c| ...}`)
* Llamando a `settings` desde dentro del ámbito de la petición

### Ámbito de Petición/Instancia

Para cada petición entrante, una nueva instancia de la clase de tu aplicación
es creada y todos los bloques de rutas son ejecutados en ese ámbito. Desde este
ámbito podés acceder a los objetos `request` y `session` o llamar a los métodos
de renderización como `erb` o `haml`. Podés acceder al ámbito de la aplicación
desde el ámbito de la petición utilizando `settings`:

```ruby
class MiApp < Sinatra::Base
  # Ey, estoy en el ámbito de la aplicación!
  get '/definir_ruta/:nombre' do
    # Ámbito de petición para '/definir_ruta/:nombre'
    @valor = 42

    settings.get("/#{params['nombre']}") do
      # Ámbito de petición para "/#{params['nombre']}"
      @valor # => nil (no es la misma petición)
    end

    "Ruta definida!"
  end
end
```

Tenés la ligadura al ámbito de la petición dentro de:

* bloques pasados a get/head/post/put/delete/options
* filtros before/after
* métodos ayudantes
* plantillas/vistas

### Ámbito de Delegación

El ámbito de delegación solo reenvía métodos al ámbito de clase. De cualquier
manera, no se comporta 100% como el ámbito de clase porque no tenés la ligadura
de la clase: únicamente métodos marcados explícitamente para delegación están
disponibles y no compartís variables/estado con el ámbito de clase (léase:
tenés un `self` diferente). Podés agregar delegaciones de método llamando a
`Sinatra::Delegator.delegate :nombre_del_metodo`.

Tenés la ligadura al ámbito de delegación dentro de:

* La ligadura del top-level, si hiciste `require "sinatra"`
* Un objeto extendido con el mixin `Sinatra::Delegator`

Hechale un vistazo al código: acá está el
[Sinatra::Delegator mixin](https://github.com/sinatra/sinatra/blob/ca06364/lib/sinatra/base.rb#L1609-1633)
que [extiende el objeto main](https://github.com/sinatra/sinatra/blob/ca06364/lib/sinatra/main.rb#L28-30).

## Línea de Comandos

Las aplicaciones Sinatra pueden ser ejecutadas directamente:

```shell
ruby miapp.rb [-h] [-x] [-e ENTORNO] [-p PUERTO] [-o HOST] [-s MANEJADOR]
```

Las opciones son:

```
-h # ayuda
-p # asigna el puerto (4567 es usado por defecto)
-o # asigna el host (0.0.0.0 es usado por defecto)
-e # asigna el entorno (development es usado por defecto)
-s # especifica el servidor/manejador rack (thin es usado por defecto)
-x # activa el mutex lock (está desactivado por defecto)
```

### Multi-threading

_Basado en [esta respuesta en StackOverflow][so-answer] escrita por Konstantin_

Sinatra no impone ningún modelo de concurrencia, sino que lo deja en manos del
handler Rack que se esté usando (Thin, Puma, WEBrick). Sinatra en sí mismo es
thread-safe, así que no hay problema en que el Rack handler use un modelo de
concurrencia basado en hilos.

Esto significa que, cuando estemos arrancando el servidor, tendríamos que
especificar la opción adecuada para el handler Rack específico. En este ejemplo
vemos cómo arrancar un servidor Thin multihilo:

```ruby
# app.rb

require 'sinatra/base'

class App < Sinatra::Base
  get '/' do
    "¡Hola, Mundo!"
  end
end

App.run!
```

Para arrancar el servidor, el comando sería:

```shell
thin --threaded start
```

[so-answer]: http://stackoverflow.com/questions/6278817/is-sinatra-multi-threaded/6282999#6282999)

## Requerimientos

Las siguientes versiones de ruby son las oficialmente soportadas por el equipo de Sinatra
<dl>
  <dt>Ruby 2.2</dt>
  <dd>
    2.2 es la recomendada y no existen planes de quitarle el soporte.
  </dd>

  <dt>Rubinius</dt>
  <dd>
    Tiene soporte oficial (Rubinius >= 2.x). Se recomienda usarlo con
    <tt>gem install puma</tt>.
  </dd>

  <dt>JRuby</dt>
  <dd>
  	La lultima version de Jruby tiene soporte oficial. No es recomendable
  	usar extenciones en C con JRuby. Es recomendado correr con
    <tt>gem install trinidad</tt>.
  </dd>
</dl>

Versiones de ruby anteriores a 2.2.2 no tienen más soporte en Sinatra 2.0.

Siempre le prestamos atención a las nuevas versiones de Ruby.

Las siguientes implementaciones de ruby ya no son más soportadas oficialmente pero siguen siendo conocidas por Sinatra:

* Viejas versiones de JRuby and Rubinius
* Ruby Enterprise Edition
* MacRuby, Maglev, IronRuby
* Ruby 1.9.0 y 1.9.1 (pero no recomendamos su uso)

No ser soportada oficialmente, significa que si las cosas se rompen
ahí y no en una plataforma soportada, asumimos que no es nuestro problema sino
el suyo.

Nuestro servidor CI también se ejecuta sobre ruby-head (Las próximas versiones de MRI),  Como están en movimiento constante, no podemos
garantizar nada. De todas formas se espera que las próximas versiones de
Ruby 2.x sean soportadas.

Sinatra debería funcionar en cualquier sistema operativo soportado por la
implementación de Ruby elegida.

Si te encuentras utilizando MacRuby, deberias instalar `gem install control_tower`.

De momento no se puede ejecutar sinatra en Cardinal, SmallRuby, BlueRuby ni tampoco en las versiones de ruby anteriores a 2.2.

## A la Vanguardia

Si querés usar el código de Sinatra más reciente, sentite libre de ejecutar
tu aplicación sobre la rama master, en general es bastante estable.

También liberamos prereleases de vez en cuando, así, podés hacer:

```shell
gem install sinatra --pre
```

Para obtener algunas de las últimas características.

### Con Bundler

Esta es la manera recomendada para ejecutar tu aplicación sobre la última
versión de Sinatra usando [Bundler](http://bundler.io).

Primero, instalá Bundler si no lo hiciste todavía:

```shell
gem install bundler
```

Después, en el directorio de tu proyecto, creá un archivo `Gemfile`:

```ruby
source :rubygems
gem 'sinatra', :git => "git://github.com/sinatra/sinatra.git"

# otras dependencias
gem 'haml'                    # por ejemplo, si usás haml
gem 'activerecord', '~> 3.0'  # quizás también necesités ActiveRecord 3.x
```

Tené en cuenta que tenés que listar todas las dependencias directas de tu
aplicación. No es necesario listar las dependencias de Sinatra (Rack y Tilt)
porque Bundler las agrega directamente.

Ahora podés arrancar tu aplicación así:

```shell
bundle exec ruby miapp.rb
```

### Con Git

Cloná el repositorio localmente y ejecutá tu aplicación, asegurándote que el
directorio `sinatra/lib` esté en el `$LOAD_PATH`:

```shell
cd miapp
git clone git://github.com/sinatra/sinatra.git
ruby -Isinatra/lib miapp.rb
```

Para actualizar el código fuente de Sinatra en el futuro:

```shell
cd miapp/sinatra
git pull
```
### Instalación Global

Podés construir la gem vos mismo:

```shell
git clone git://github.com/sinatra/sinatra.git
cd sinatra
rake sinatra.gemspec
rake install
```

Si instalás tus gems como root, el último paso debería ser

```shell
sudo rake install
```
## Versionado

Sinatra utiliza el [Versionado Semántico](http://semver.org/),
siguiendo las especificaciones SemVer y SemVerTag.

## Lecturas Recomendadas

* [Sito web del proyecto](http://www.sinatrarb.com/) - Documentación
  adicional, noticias, y enlaces a otros recursos.
* [Contribuyendo](http://www.sinatrarb.com/contributing) - ¿Encontraste un
  error?. ¿Necesitás ayuda?. ¿Tenés un parche?.
* [Seguimiento de problemas](https://github.com/sinatra/sinatra/issues)
* [Twitter](https://twitter.com/sinatra)
* [Lista de Correo](http://groups.google.com/group/sinatrarb/topics)
* [IRC: #sinatra](irc://chat.freenode.net/#sinatra) en http://freenode.net
* [Sinatra Book](https://github.com/sinatra/sinatra-book/) Tutorial (en inglés).
* [Sinatra Recipes](http://recipes.sinatrarb.com/) Recetas contribuidas
  por la comunidad (en inglés).
* Documentación de la API para la
  [última versión liberada](http://www.rubydoc.info/gems/sinatra) o para la
  [rama de desarrollo actual](http://www.rubydoc.info/github/sinatra/sinatra)
  en http://www.rubydoc.info/
* [Servidor de CI](https://travis-ci.org/sinatra/sinatra)