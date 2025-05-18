package com.certidevs.reto.entities;

import javax.annotation.processing.Generated;

import jakarta.persistence.Entity;
import jakarta.persistence.Table;
import jakarta.persistence.Id;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.GeneratedValue;

@Entity
@Table(name = "tareas")
public  class Tarea {

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
private String titulo;
private String descripcion;
private Boolean completada;

@ManyToOne
@JoinColumn(name = "proyecto_id")
private Proyecto proyecto;

// Constructor vacio
public Tarea() {}

// Constructor con parametros

public Tarea(String titulo, String descripcion, Boolean completada, Proyecto proyecto) {

this.nombre = titulo;
this.descripcion = descripcion;
this.completada = completada;
this.proyecto = proyecto;

}
// Getters y setters

public Long getId() {
  return id;
}

public void setId(Long id) {
  this.id = id;
}
public String getTitulo() {
  return titulo;
}

public void setTitulo(String Titulo) {
  this.titulo = titulo;
}

public String getDescripcion() {
  return descripcion;
}

public void setDescripcion(String descripcion) {
  this.descripcion = descripcion;
}

public Boolean getCompletada() {
  return completada;
}

public void setCompletada(Boolean completada) {
  this.completada = completada;
}

public Proyecto getProyecto() {
  return proyecto;
}

public void setProyecto(Proyecto proyecto) {
  this.proyecto = proyecto;
}

// Metodo toString

@Override
public String toString() {
  return "Tarea [id=" + id + ", titulo=" + titulo + ", descripcion=" + descripcion + ", completada=" + completada +  ", proyecto=" + proyecto + "]";
}

}

package com.certidevs.reto.entities;

import javax.annotation.processing.Generated;

import jakarta.persistence.Entity;
import jakarta.persistence.Table;
import jakarta.persistence.Id;
import jakarta.persistence.GeneratedValue;

@Entity
@Table(name = "proyectos")
public  class Proyecto {

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
private String nombre;
private String descripcion;
private LocalDate fechaInicio;
private Boolean activo;

// Constructor vacio
public Proyecto() {}

// Constructor con parametros

public Proyecto(String nombre, String descripcion, LocalDate fechaInicio, Boolean activo) {

this.nombre = nombre;
this.descripcion = descripcion;
this.fechaInicio = fechaInicio;
this.activo = activo;

}

// Getters y setters

public Long getId() {
  return id;
}

public void setId(Long id) {
  this.id = id;
}
public String getNombre() {
  return nombre;
}

public void setNombre(String Nombre) {
  this.nombre = nombre;
}

public String getDescripcion() {
  return descripcion;
}

public void setDescripcion(String descripcion) {
  this.descripcion = descripcion;
}

public LocalDate getFechaInicio() {
  return fechaInicio;
}

public void setFechaInicio(LocalDate fechaInicio) {
  this.fechaInicio = fechaInicio;
}

public Boolean getActivo() {
  return activo;
}

public void setActivo(Boolean activo) {
  this.activo = activo;
}

// Metodo toString

@Override
public String toString() {
  return "Proyecto [id=" + id + ", nombre=" + nombre + ", descripcion=" + descripcion + ", fechaInicio=" + fechaInicio +  ", activo=" + activo + "]";
}

}

package com.certidevs.reto.repositories;

import javax.annotation.processing.Generated;

import jakarta.persistence.Entity;
import jakarta.persistence.Table;
import jakarta.persistence.Id;
import jakarta.persistence.GeneratedValue;


public interface proyectoRepository extends JpaRepository<Proyecto, Long> {

// metodo derivado para buscar proyectos por nombre

Proyecto findByNombreIgnoreCase(String nombre);

// metodo derivado para buscar proyectos por fecha de inicio

List<Proyecto> findByFechaInicio(LocalDate fechaInicio);

// consulta JPQL que encuentre todos los los proyectos activos

@Query("select p from Proyecto p where p.activo = true")
List<Proyecto> findByActivoTrue();



}

package com.certidevs.reto.repositories;

import com.certidevs.reto.entities.Tarea;
import org.springframework.data.jpa.repository.JpaRepository;



public interface tareaRepository extends JpaRepository<Tarea, Long> {
 
// metodo derivado para buscar tareas por titulo

Tarea findByTitulo(String titulo);

// metodo derivado para contar cuantas areas no estan completadas

long countByCompletadaFalse();

// consulta JPQL que encuentre todas las tareas que pertenezcan a un mismo proyecto

@Query("select t from Tarea t where t.projecto.id = ?1")
List <Tarea> findByProyecto_Id(Long id);
    

}

package com.certidevs.reto;

import javax.annotation.processing.Generated;

import jakarta.persistence.Entity;
import jakarta.persistence.Table;
import jakarta.persistence.Id;
import jakarta.persistence.GeneratedValue;

import com.certidevs.reto.entities.Tarea;
import org.springframework.data.jpa.repository.JpaRepository;


@SpringBootApplication

public interface RetoApplication {

// inicializar spring
    ApplicationContext spring = SpringApplication.run(RetoApplication.class, args);

// obtener repositorios
    ProyectoRepository proyectoRepository = spring.getBean(ProyectoRepository.class);
    TareaRepository tareaRepository = spring.getBean(TareaRepository.class);

// crear proyectos

Proyecto proyecto1 = new Proyecto(nombre= "Web App", descripcion= "Aplicacion Web", LocalDate.of(year= 2025, month= 1, dayOfMonth= 18), activo= true);

Proyecto proyecto2 = new Proyecto(nombre= "Mobile App", descripcion= "Aplicacion para Android", LocalDate.of(year= 2025, month= 3, dayOfMonth= 1), activo= false);

// guardar proyectos

RetoApplication.saveAll List.of (proyecto1, proyecto2);

// crear tareas

Tarea tarea1 = new Tarea(titulo= "Diseño de la interfaz", descripcion= "Diseñar interfaz", completada= true, proyecto1);
Tarea tarea2 = new Tarea(titulo= "Implementacion backend", descripcion= "Implementar logica de backend", completada= false, proyecto1);
Tarea tarea3 = new Tarea(titulo= "Configurar base de datos", descripcion= "descripcion 3", completada= true, proyecto2);
Tarea tarea4 = new Tarea(titulo= "Escribir documentación", descripcion= "descripcion 4", completada= false, proyecto1);
Tarea tarea5 = new Tarea(titulo= "Pruebas unitarias", descripcion= "descripcion 5", completada= true, proyecto2);

// guardar las tareas

RetoApplication.saveAll List.of (tarea1, tarea2, tarea3, tarea4, tarea5);

// probar metodos y consultas

String nombreProyectoBuscado = "Web App";
Proyecto proyectoEncontrado = proyectoRepository.findByNombre(nombreProyectoBuscado);
if (proyectoEncontrado != null) {

System.out.println("Proyecto encontrado por nombre: " + nombreProyectoBuscado + " : " + proyectoEncontrado.getNombre);


} else {

System.out.println("No se encontro ningun proyecto con el nombre: " + nombreProyectoBuscado);


}

LocalDate fechaBuscada = LocalDate.of(year= 2025, month= 1, dayOfMonth= 18, activo= true);
List<Proyecto> proyectosPorFecha = proyectoRepository.findByFechaInicio(fechaBuscada);
System.out.println("Proyectos con fecha de inicio: " + fechaBuscada);
if(proyectosPorFecha.isEmpty()) {

System.out.println("No se han encontrado proyectos en la fecha: " + fechaBuscada);


} else {

for (Proyecto proyecto : proyectosPorFecha) {

System.out.println(proyecto.getNombre() + ":" + proyecto.getDescripcion());

}


}

List<Proyecto> proyectosActivos = proyectoRepository.findByActivoTrue();
if (proyectosActivos.isEmpty()) {

System.out.println("No se han encontrado proyectos activos: ");


} else {

for (Proyecto proyecto  : proyectosActivos) {

System.out.println(proyecto.getNombre() + " : " + proyecto.getDescripcion());


}

}


String tituloBuscado = "Diseño de la interfaz";
Tarea tareaEncontrada = tareaRepository.findByTitulo(tituloBuscado);
if (tareaEncontrada != null) {

System.out.println("Tarea encontrada por titulo " + tituloBuscado + " : " + tareaEncontrada.getTitulo());


}

long tareasNoCompletadas = tareaRepository.countByCompletadaFalse();
System.out.println("Tareas a completar: " + tareasNoCompletadas);

Long proyectoId = proyecto1.getId();
List<Tarea> tareasProyecto = tareaRepository.findByProyecto_Id(proyectoId);
if(tareasProyecto.isEmpty()) {

System.out.println("El proyecto con id" + proyectoId + "no tiene tareas");



} else {

System.out.println("Tareas del proyecto con id" + proyectoId + " : ");

for(Tarea tarea : tareasProyecto) {

system.out.println(tarea.getTitulo() + " : " + (tarea.getCompletada() ? "Completada" : "No completada"));


}


}

}






