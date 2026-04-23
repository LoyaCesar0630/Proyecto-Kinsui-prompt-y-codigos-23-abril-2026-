¡Hola! Es un gusto saludarte. Como desarrollador de software, entiendo que integrar Flutter con Firebase bajo una metodología de agentes como **Antigravity** requiere una estructura clara y modular.

Para este proyecto, vamos a construir un sistema CRUD de empleados donde cada parte del proceso (validación, conexión a base de datos y UI) se trate como una responsabilidad específica.

---

## 🏗️ Fase 1: Configuración del Entorno y Firebase

### 1. Creación del Proyecto
Abre tu terminal y ejecuta:
```bash
flutter create crudclinica
cd crudclinica
```

### 2. Configuración en la Consola de Firebase
1. Ve a [Firebase Console](https://console.firebase.google.com/).
2. Crea un proyecto nuevo llamado `crudclinica`.
3. En el menú lateral, selecciona **Firestore Database** y haz clic en "Crear base de datos".
4. Elige el modo de prueba (para facilitar el desarrollo inicial).
5. Registra tu app (Android/iOS) siguiendo los pasos de la consola para descargar el archivo `google-services.json` (Android) o `GoogleService-Info.plist` (iOS).

### 3. Librerías Necesarias (`pubspec.yaml`)
Agrega estas dependencias para habilitar Firebase y la arquitectura de agentes:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.24.2
  cloud_firestore: ^4.14.0
  # Antigravity se usa para la orquestación de lógica de negocio (agentes)
  antigravity: ^1.0.0 
```
*Ejecuta `flutter pub get` en la terminal.*

---

## 🧠 Fase 2: Metodología de Agentes (Antigravity)

En **Antigravity**, no solo escribimos funciones; diseñamos entidades que "saben" hacer cosas.

| Componente | Definición en este Proyecto |
| :--- | :--- |
| **Agente (EmployeeAgent)** | El cerebro que decide cuándo guardar o borrar un dato. |
| **Rol (DatabaseOperator)** | Define que el agente tiene permiso para tocar Firestore. |
| **Skill (CRUD Operations)** | Las habilidades específicas: `create()`, `read()`, `update()`, `delete()`. |
| **Flujo (Workflow)** | La secuencia: UI -> Agente -> Firestore -> UI. |



---

## 📂 Estructura de Carpetas

```text
crudclinica/
├── lib/
│   ├── agents/
│   │   └── employee_agent.dart    # Lógica y Reglas de negocio
│   ├── models/
│   │   └── employee_model.dart    # Estructura (Nombre, Edad, Salario)
│   ├── services/
│   │   └── firebase_service.dart  # Conexión pura a Firestore
│   ├── ui/
│   │   └── employee_page.dart     # Interfaz de usuario
│   └── main.dart                  # Punto de entrada
```

---

## 💻 Implementación del Código (Funcional)

### 1. El Modelo (`lib/models/employee_model.dart`)
```dart
class Employee {
  String? id;
  String name;
  int age;
  double salary;

  Employee({this.id, required this.name, required this.age, required this.salary});

  Map<String, dynamic> toMap() => {
    "name": name,
    "age": age,
    "salary": salary,
  };

  factory Employee.fromMap(String id, Map<String, dynamic> map) => Employee(
    id: id,
    name: map['name'],
    age: map['age'],
    salary: map['salary'].toDouble(),
  );
}
```

### 2. El Agente y Skills (`lib/agents/employee_agent.dart`)
Aquí implementamos la lógica siguiendo la filosofía de **Antigravity**.

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/employee_model.dart';

class EmployeeAgent {
  final CollectionReference _db = FirebaseFirestore.instance.collection('employees');

  // Skill: Create
  Future<void> createEmployee(Employee emp) async {
    await _db.add(emp.toMap());
  }

  // Skill: Read (Stream para tiempo real)
  Stream<List<Employee>> get employeesStream {
    return _db.snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Employee.fromMap(doc.id, doc.data() as Map<String, dynamic>)).toList());
  }

  // Skill: Update
  Future<void> updateEmployee(Employee emp) async {
    await _db.doc(emp.id).update(emp.toMap());
  }

  // Skill: Delete
  Future<void> deleteEmployee(String id) async {
    await _db.doc(id).delete();
  }
}
```

### 3. La Interfaz (`lib/ui/employee_page.dart`)
```dart
import 'package:flutter/material.dart';
import '../models/employee_model.dart';
import '../agents/employee_agent.dart';

class EmployeePage extends StatefulWidget {
  @override
  _EmployeePageState createState() => _EmployeePageState();
}

class _EmployeePageState extends State<EmployeePage> {
  final EmployeeAgent _agent = EmployeeAgent();
  final _nameController = TextEditingController();
  final _ageController = TextEditingController();
  final _salaryController = TextEditingController();

  void _showForm(Employee? employee) {
    if (employee != null) {
      _nameController.text = employee.name;
      _ageController.text = employee.age.toString();
      _salaryController.text = employee.salary.toString();
    }

    showModalBottomSheet(
      context: context,
      builder: (_) => Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(controller: _nameController, decoration: InputDecoration(labelText: 'Nombre')),
            TextField(controller: _ageController, decoration: InputDecoration(labelText: 'Edad'), keyboardType: TextInputType.number),
            TextField(controller: _salaryController, decoration: InputDecoration(labelText: 'Salario'), keyboardType: TextInputType.number),
            ElevatedButton(
              onPressed: () {
                final emp = Employee(
                  id: employee?.id,
                  name: _nameController.text,
                  age: int.parse(_ageController.text),
                  salary: double.parse(_salaryController.text),
                );
                if (employee == null) _agent.createEmployee(emp);
                else _agent.updateEmployee(emp);
                Navigator.pop(context);
              },
              child: Text(employee == null ? 'Crear' : 'Actualizar'),
            )
          ],
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('CRUD Clínica - Empleados')),
      body: StreamBuilder<List<Employee>>(
        stream: _agent.employeesStream,
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          return ListView.builder(
            itemCount: snapshot.data!.length,
            itemBuilder: (context, index) {
              final emp = snapshot.data![index];
              return ListTile(
                title: Text(emp.name),
                subtitle: Text('Edad: ${emp.age} - Salario: \$${emp.salary}'),
                trailing: Row(
                  mainAxisSize: MainAxisSize.min,
                  children: [
                    IconButton(icon: Icon(Icons.edit), onPressed: () => _showForm(emp)),
                    IconButton(icon: Icon(Icons.delete), onPressed: () => _agent.deleteEmployee(emp.id!)),
                  ],
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => _showForm(null),
      ),
    );
  }
}
```

### 4. Punto de Entrada (`lib/main.dart`)
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'ui/employee_page.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  runApp(MaterialApp(
    home: EmployeePage(),
    theme: ThemeData(primarySwatch: Colors.blue),
  ));
}
```

---

## 🎯 Resumen del Flujo de Trabajo
1.  **Agente de Datos**: Actúa como el intermediario (EmployeeAgent).
2.  **Rol**: El agente asume el rol de "Administrador de Firestore".
3.  **Skills**: Ejecuta funciones asíncronas para modificar la nube.
4.  **Flujo**: El usuario interactúa con la UI, el Agente procesa la solicitud y Firebase refleja los cambios en tiempo real mediante el `StreamBuilder`.

¿Te gustaría que profundicemos en cómo manejar errores de conexión o prefieres pasar a la personalización de estilos?
