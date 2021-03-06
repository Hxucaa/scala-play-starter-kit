# Scala-Play-Boilerplate


## How to start?

1. Install docker and docker-compose.
2. Run `docker-compose up -d` in root directory to launch an instance of PostgreSQL.
3. Run `sbt db_migrate` to set up schema in PostgreSQL.
4. (Optional) Run `sbt populate` to populate database in development environment with fixtures.
5. Run `sbt run` to start the `Play` application.
 

## Libraries Used
- Java8
- Play Framework
- [Silhouette](https://www.silhouette.rocks)
- PostgreSQL
- Flyway
- Quill
- Enumeratum
- Circe
- Cats
- Shapeless
- PureConfig



## Notes
### Quill
```scala
val pagedQuery = quote { (offset: Int,limit: Int) => 
  query[Record].drop(offset).take(limit)
}
db.run(pagedQuery)(offset, limit)
```

Another possibility using lift and implicit quotation:
```scala
def pagedQuery(offset: Int,limit: Int) = 
db.run(query[Record].drop(lift(offset)).take(lift(limit)))
```
Also, you should avoid explicit typing your quotation with : `Quoted[...]` because it forces the quotation to be dynamic.

### Authentication

Silhouette provides authentication for the application.

#### Error handlers

##### Global error handlers

By default Silhouette provides default error handlers via Guice dependency injection. To disable the default error 
handler, edit `application.conf` with the following content
```hocon
play.modules.disabled += "com.mohiva.play.silhouette.api.actions.SecuredErrorHandlerModule"
```
Please refer to the [list of error handlers](https://www.silhouette.rocks/docs/endpoints#section-list-of-error-handlers) 
section for an overview of all available error handlers and their modules.

Once the default handler module is disabled, you can bind your own implementation.

Global custom error handlers, `CustomSecuredErrorHandler` and `CustomUnsecuredErrorHandler`, redirect unauthenticated 
or unauthorized accesses to the appropriate landing pages.

##### Local error handlers

#### Adding authorization logic to secured endpoints

Silhouette provides a way do add additional authorization logic to secured endpoints. This is done by implementing an 
`Authorization` object that is passed to all `SecuredRequestHandler` and `SecuredAction` as a parameter. 
`utility.authentication.AllowProvider` checks whether a user logged in using a specific login provider. Could be useful for 
only exposing contents to specific login method.

#### Silhouette Events

Silhouette provides several events and event handling based on 
[Akka's Event Bus](http://doc.akka.io/docs/akka/2.2.4/scala/event-bus.html). Silhouette itself doesn't listen for events.
It is up to developers to decide if an event is useful or not.


## PostgreSQL in Docker

Run `docker-compose up -d` to launch a PostgreSQL instance for development. By default the database can be connected to 
at `localhost:27001`.

## Database Migration

When the `Play` application is launched, database migration is checked. If migration is not up to the latest version, 
an error message is displayed on the web page. Click on the `Apply this script` button to perform migration.

Alternatively, a manual migration can be performed via sbt. `sbt db_migrate`.

## Database Population

Run `sbt populate` to populate database in development environment with fixtures.

### TODO
1. Integrate with sbt-docker perhaps??

## IntelliJ Idea Integration
[doc](https://playframework.com/documentation/2.6.3/IDE#Navigate-from-an-error-page-to-the-source-code)

Set up Play to add hyperlinks to an error page. This will link to runtime exceptions thrown when Play is running in 
development mode.

## [Coursier](https://github.com/coursier/coursier) Scala artifact fetching

Coursier is a dependency resolver / fetcher for Maven / Ivy. It can download artifacts in parallel.

## [Scala Formatter](https://github.com/scalameta/scalafmt)

A Scala formatter configuration file `.scalafmt.conf` is included in the project. Only git included files are formatted.

The sbt plugin, [neo-sbt-scalafmt](https://github.com/lucidsoftware/neo-sbt-scalafmt) is used to integrate formatter 
with sbt. For compatibility with `sbt 0.13`, a workaround is used in `build.sbt`.

To format source code manually, run `sbt scalafmt` to format source code.

## [Scala WartRemover](https://github.com/wartremover/wartremover)

At compile time, WartRemover

# TODO

1. Update cats to 1.0.0 when released.
2. Update circe to 1.0.0 when its dependency on cats is updated.
3. Update slick-pg's when circe is updated.
4. Dev, test, prod environment.
5. Test code.
6. Hikari config.
7. Figure out a way to no longer require user to create `Summary.scala` file manually after cloning new project.
