import sqlite3
from tkinter import *
from tkinter import messagebox
from tkinter import ttk


#-----------------------------------------VALIDACIONES------------------------------------------


def validate_number(text):
    return text.isdecimal()


#-----------------------------------------FUNCIONES------------------------------------------


def conexionBBDD():
    miConexion = sqlite3.connect("SEGURIDAD_SOCIAL")
    miCursor = miConexion.cursor()

    try:
        miCursor.execute("CREATE TABLE DATOS_PERSONAS (ID INT (10)  PRIMARY KEY ,NOMBRE_CONTACTO VARCHAR (50),APELLIDO VARCHAR (50),SALUD VARCHAR (50), PENSION VARCHAR (20), RIESGOS_LABORALES VARCHAR(50), CAJA_COMPENSACION VARCHAR(70))")

        messagebox.showinfo("BBDD","BBDD creada con éxtio")

    except:

        messagebox.showwarning("¡ATENCIÓN!","la BBDD ya existe")



    miConexion.close()

def salirAplicacion():
    valor = messagebox.askquestion("Salir","¿Desea salir de la aplicación?")

    if (valor=="yes"):
        root.destroy()

def limpiarCampos():
    miId.set("")
    miNombre.set("")
    miApellido.set("")
    miSalud.set("")
    miPension.set("")
    miRiesgos.set("")
    miCaja.set("")






def guardar():

    try:
        miConexion = sqlite3.connect("SEGURIDAD_SOCIAL")
        miCursor = miConexion.cursor()

        miID2 = miId.get()
        miNombre2 = miNombre.get()
        miApellido2 = miApellido.get()
        miSalud2 = miSalud.get()
        miPension2 = miPension.get()
        miRiesgos2 = miRiesgos.get()
        miCaja2 = miCaja.get()

        if ((miID2 != "") and (miNombre2 != "") and (miApellido2 != "") and (miSalud2 != "") and (miPension2 != "") and (miRiesgos2 != "") and (miCaja2 != "")):

            datos = miId.get(),miNombre.get(),miApellido.get(),miSalud.get(),miPension.get(),miRiesgos.get(),miCaja.get()

        
            miCursor.execute("INSERT INTO DATOS_PERSONAS VALUES(?,?,?,?,?,?,?)",(datos))

            miConexion.commit()

            messagebox.showinfo("BBDD","Persona guardado con éxito")

            limpiarCampos()
            mostrarPersonas()
        else:
            messagebox.showerror("ERROR","Debe llenar todos los campos")


    except FileNotFoundError:
        messagebox.showwarning("¡ATENCIÓN!","Debe conectar la base de datos. \n Para conectarla diríjase a BBDD Y presione conectar")

    except sqlite3.OperationalError:
        messagebox.showwarning("¡ATENCIÓN!","Debe conectar la base de datos. \n Para conectarla diríjase a BBDD Y presione conectar")

    except sqlite3.IntegrityError:
        messagebox.showerror("ERROR","No pueden haber dos ID iguales")

def leer():

    try:
    
        if (miBuscaId.get() != ""):
            miConexion = sqlite3.connect("SEGURIDAD_SOCIAL")
            miCursor = miConexion.cursor()

            miCursor.execute("SELECT * FROM DATOS_PERSONAS WHERE ID="+miBuscaId.get())

            elContacto = miCursor.fetchall()

            if (not elContacto):
                messagebox.showwarning("ERROR","Persona no encontrada, verifique el ID  de la persona")

            else:
                for contacto in elContacto:
                    miId.set(contacto[0])
                    miNombre.set(contacto[1])
                    miApellido.set(contacto[2])
                    miSalud.set(contacto[3])
                    miPension.set(contacto[4])
                    miRiesgos.set(contacto[5])
                    miCaja.set(contacto[6])
                    
                
            miConexion.commit()

        else:
            messagebox.showerror("ERROR","Debe llenar el campo ID para buscar el contacto")

    except FileNotFoundError: 
        messagebox.showwarning("¡ATENCIÓN!","Debe conectar la base de datos. \n Para conectarla diríjase a BBDD Y presione conectar")

    except sqlite3.OperationalError:
        messagebox.showwarning("¡ATENCIÓN!","Debe conectar la base de datos. \n Para conectarla diríjase a BBDD Y presione conectar") 


def mostrarPersonas():
    try:
        r = Text(root, width=80, height=15)
        miConexion = sqlite3.connect("SEGURIDAD_SOCIAL")
        miCursor = miConexion.cursor()

        miCursor.execute("SELECT * FROM DATOS_PERSONAS" )
        
        lasPersonas = miCursor.fetchall()
        r.insert(INSERT, "ID\t\tNOMBRE \t\tAPELLIDO \t\tSALUD\t\tPENSION \t\tRIESGOS LABORALES \t\tCAJA DE COMPENSACIÓN \t \n")
    
        for persona in lasPersonas:
            print("ID: ",persona[0])
            print("NOMBRE: ",persona[1])
            print("APELLIDO: ",persona[2])
            print("SALUD: ",persona[3])
            print("PENSIÓN: ",persona[4])
            print("RIESGOS LABORALES: ",persona[5])
            print("CAJA DE COMPENSACIÓN: ",persona[6])
            
            

            varId = str(persona[0])
            varNombre = str(persona[1])
            varApellido = str(persona[2])
            varSalud = str(persona[3])
            varPension = str(persona[4])
            varRiesgos = str(persona[5])
            varCaja = str(persona[6])

            r.insert(INSERT,varId+"\t\t"+varNombre+"\t\t"+
            varApellido+"\t\t"+varSalud+"\t\t"+varPension+"\t \t"+varRiesgos+"\t\t\t"+varCaja+"\t\n")
        
        r.place(x=20,y=310,width=1000)
        r.config(state=DISABLED)
        
        miConexion.commit()


    except sqlite3.OperationalError:
        messagebox.showwarning("¡ATENCIÓN!","Debe conectar la base de datos. \n Para conectarla diríjase a BBDD Y presione conectar")

    

def actualizar():
    try:
        if (miBuscaId.get() != ""):
        
            miConexion = sqlite3.connect("SEGURIDAD_SOCIAL")
            miCursor = miConexion.cursor()
            

            datos = miId.get(),miNombre.get(),miApellido.get(),miSalud.get(),miPension.get(), miRiesgos.get(),miCaja.get()
            
            print(datos)
        
            miCursor.execute("UPDATE DATOS_PERSONAS SET ID = ?, NOMBRE_CONTACTO = ?,APELLIDO =?,SALUD=?,PENSION=?,RIESGOS_LABORALES=?,CAJA_COMPENSACION=?"+ "WHERE ID="+miId.get(),(datos))

            miConexion.commit()

            messagebox.showinfo("BBDD","Usuario actualizado con éxito")

            limpiarCampos()
            mostrarPersonas()
        
        else:
            messagebox.showerror("ERROR","Debe llenar el campo ID para actualizar el contacto")
    except FileNotFoundError:
        messagebox.showwarning("¡ATENCIÓN!","Debe conectar la base de datos. \n Para conectarla diríjase a BBDD Y presione conectar")
    except sqlite3.OperationalError:
        messagebox.showwarning("¡ATENCIÓN!","Para Actualizar. \n Primero debe clickear el botón buscar, para posteiormente actualizar los datos")
        



def eliminar():
    try:  
        if (miBuscaId.get() != ""):
            miConexion = sqlite3.connect("SEGURIDAD_SOCIAL")
            miCursor = miConexion.cursor()

            miCursor.execute("DELETE FROM DATOS_PERSONAS WHERE ID="+miBuscaId.get())

            messagebox.showinfo("BBDD","Usuario eliminado con éxito")

            limpiarCampos()

            miConexion.commit()

            mostrarPersonas()
        
        else:
            messagebox.showerror("ERROR","Debe llenar el campo ID para eliminar el contacto")
    except FileNotFoundError:
        messagebox.showwarning("¡ATENCIÓN!","Debe conectar la base de datos. \n Para conectarla diríjase a BBDD Y presione conectar")













root = Tk()


#-----------------------------------------BARRA DE MENÚ------------------------------------------


barraMenu = Menu(root)
root.config(menu=barraMenu,width=300,height=300)
root.resizable(0,0)
root.geometry("1120x600")


bbddMenu= Menu(barraMenu,tearoff=0)
bbddMenu.add_command(label="Conectar",command=conexionBBDD)
bbddMenu.add_command(label="Salir",command=salirAplicacion)

borrarMenu= Menu(barraMenu,tearoff=0)
borrarMenu.add_command(label="Borrar campos",command=limpiarCampos)

crudMenu= Menu(barraMenu,tearoff=0)
crudMenu.add_command(label="Guardar",command=guardar)
crudMenu.add_command(label="leer",command=leer)
crudMenu.add_command(label="Actualizar",command=actualizar)
crudMenu.add_command(label="Eliminar",command=eliminar)

ayudaMenu= Menu(barraMenu,tearoff=0)
ayudaMenu.add_command(label="Ayuda")
ayudaMenu.add_command(label="Acerca de...")


barraMenu.add_cascade(label="BBDD",menu=bbddMenu)
barraMenu.add_cascade(label="Borrar",menu=borrarMenu)
barraMenu.add_cascade(label="CRUD",menu=crudMenu)
barraMenu.add_cascade(label="Ayuda",menu=ayudaMenu)


miframe = Frame(root)
miframe.pack()

colorFondo = "#006"
colorLetra = "#FFF"
root.config(background=colorFondo)
miId= StringVar()
miNombre = StringVar()
miApellido = StringVar()
miSalud = StringVar()
miEmail = StringVar()
miPension = StringVar()
miRiesgos = StringVar()
miCaja = StringVar()
miBuscaId = StringVar()

#-----------------------------------------CAJAS DE TEXTO------------------------------------------


cuadroId = ttk.Entry(
    validate="key",
    textvariable=miId,

    validatecommand=(root.register(validate_number), "%S")
)

cuadroId.place(x=150, y=50)


cuadroNombre = Entry(root,textvariable=miNombre,fg="BLACK").place(x=150, y=80)
cuadroApellido = Entry(root,textvariable=miApellido,fg="BLACK").place(x=150, y=110)
cuadroSalud = Entry(root,textvariable=miSalud,fg="BLACK").place(x=150, y=140)
cuadroPension = Entry(root,textvariable=miPension,fg="BLACK").place(x=150, y=170)
cuadroRiesgos = Entry(root,textvariable=miRiesgos,fg="BLACK",width=25).place(x=200, y=200)
cuadroCaja = Entry(root,textvariable=miCaja,fg="BLACK",width=30).place(x=200, y=230)

buscaID  = ttk.Entry(
     validate="key",
    textvariable=miBuscaId,

    validatecommand=(root.register(validate_number), "%S")

)
buscaID.place(x=730,y=50)

#-----------------------------------------LABELS------------------------------------------

etiquetaTitulo = Label(root, text="DATOS DE SEGURIDAD SOCIAL",bg="#006", fg=colorLetra).place(x=270,y=10)
etiquetaId = Label(root,text="ID:",bg=colorFondo,fg=colorLetra).place(x=50,y=50)
etiquetaNombre = Label(root,text="NOMBRE:",bg=colorFondo,fg=colorLetra).place(x=50,y=80)
etiquetaApellido = Label(root,text="APELLIDO:",bg=colorFondo,fg=colorLetra).place(x=50,y=110)
etiquetaSalud = Label(root,text="SALUD:",bg=colorFondo,fg=colorLetra).place(x=50,y=140)
etiquetaPension = Label(root,text="PENSIÓN:",bg=colorFondo,fg=colorLetra).place(x=50,y=170)
etiquetaRiesgos = Label(root,text="RIESGOS LABORALES:",bg=colorFondo,fg=colorLetra).place(x=50,y=200)
etiquetaCaja = Label(root,text="CAJA DE COMPENSACIÓN:",bg=colorFondo,fg=colorLetra).place(x=40,y=230)
etiquetaConsultaId = Label(root,text="ID: ",bg=colorFondo,fg=colorLetra).place(x=700,y=50)


#---------------------------------------BOTONES...................................................

botonVerContactos = Button(root,text="Ver Personas",bg="#009",fg="white",command=mostrarPersonas).place(x=70,y=270)
botonGuardar = Button(root,text="Guardar",bg="#009",fg="white",command=guardar).place(x=170,y=270)
botonLimpiarCampos = Button(root,text="Limpiar",bg="#009",fg="white",command=limpiarCampos).place(x=240,y=270)
botonBuscar = Button(root,text="Buscar",bg="#009",fg="white",command=leer).place(x=700,y=80)
botonActualizar = Button(root,text="Actualizar",bg="#009",fg="white",command=actualizar).place(x=750,y=80)
botonEliminar = Button(root,text="Eliminar",bg="#009",fg="white",command=eliminar).place(x=830,y=80)


root.mainloop()