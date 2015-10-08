# Proxy
﻿Para explicar la motivación del uso de este patrón veamos un escenario donde su aplicación sería la solución más adecuada al problema planteado.
 Consideremos un editor que puede incluir objetos gráficos dentro de un documento. Se requiere que la apertura de un documento sea rápida, 
mientras que la creación de algunos objetos (imágenes de gran tamaño) es cara. En este caso no es necesario crear todos los objetos con 
imágenes nada más abrir el documento porque no todos los objetos son visibles. Interesa por tanto retrasar el coste de crear e inicializar 
un objeto hasta que es realmente necesario (por ejemplo, no abrir las imágenes de un documento hasta que no son visibles). La solución que se 
plantea para ello es la de cargar las imágenes bajo demanda. Pero, ¿cómo cargar las imágenes bajo demanda sin complicar el resto del editor?
 La respuesta es utilizar un objeto proxy. Dicho objeto se comporta como una imagen normal y es el responsable de cargar la imagen bajo demanda.

El patrón proxy se usa cuando se necesita una referencia a un objeto más flexible o sofisticada que un puntero. Dependiendo de la función que se
desea realizar con dicha referencia podemos distinguir diferentes tipos de proxies:

proxy remoto: representante local de un objeto remoto.
proxy virtual: crea objetos costosos bajo demanda (como la clase ImagenProxy en el ejemplo de motivación).
proxy de protección: controla el acceso al objeto original (ejemplo de proxy de protección: [1])
proxy de referencia inteligente: sustituto de un puntero que lleva a cabo operaciones adicionales cuando se accede a un objeto
 (ej. contar número de referencias al objeto real, cargar un objeto persistente bajo demanda en memoria, control de concurrencia de acceso 
tal como bloquear el objeto para impedir acceso concurrente, …).


public class Cliente {



  public Cliente(Sujeto sujeto) {
       _sujeto = sujeto; 
  }

  public void ejecutar() {
        _sujeto.metodo1();
        _sujeto.metodo1();
        _sujeto.metodo2();
        _sujeto.metodo1();
  }
}
     private Sujeto _sujeto;
      
 }
   public abstract class Sujeto {

     private Sujeto(String nombre) { 
         _nombre = nombre; 
    }
 
     public String toString() { 
          return _nombre; 
    }

     public abstract void metodo1();
     public abstract void metodo2();

     public void status (Sujeto sujeto) {
         Proxy p;
         p = (Proxy) sujeto;
         p.toString();
     }   
 
     private String  _nombre;
}

public class Proxy extends Sujeto {

  public Proxy (String nombre) {
     super(nombre);
    _sujetoReal = null;
   _accesosMetodo1 = 0;
   _accesosMetodo2 = 0;
  }


  private SujetoReal obtenerSujetoReal() {
    if (_sujetoReal == null)
      _sujetoReal = new SujetoReal(this + " (Real)");

    return _sujetoReal;
  }

  public void metodo1() { 
    _accesosMetodo1++; 
    obtenerSujetoReal().metodo1(); 
  }

  public void metodo2() { 
    _accesosMetodo2++;
    obtenerSujetoReal().metodo2(); 
  }

  public void toString() {
    if (_sujetoReal != null)
      System.out.println("Accesos a " + _sujetoReal + 
                         ": metodo1=" + _accesosMetodo1 + 
                         ", metodo2=" + _accesosMetodo2);
    else 
      System.out.println("Sujeto Real (" + this + ") no creado.");
  }

  private SujetoReal _sujetoReal; 
  private int  _accesosMetodo1, _accesosMetodo2;
}


public class SujetoReal extends Sujeto {

  public SujetoReal(String nombre) { 
     super(nombre); 

  }

  public void metodo1()  {
      System.out.println("Ejecutando metodo1 en " + this);
   }

  public void metodo2() {
      System.out.println("Ejecutando metodo2 en " + this);
   }

}


public class Main {

   public static void main(String argv[]) {

    Sujeto objetoA = new Proxy("objetoA");
    Cliente c = new Cliente(objetoA);
    objetoA.status(objetoA);
    c.ejecutar();
    objetoA.status(objetoA);

  
    Sujeto objetoB = new sujetoReal(“objetoB”);
    Cliente d = new Cliente(objetoB);
    
    d.ejecutar();
  }

}
