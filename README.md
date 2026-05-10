# Atividade-sos
## 1️⃣ pubspec.yaml

Adicione a dependência:

```yaml
dependencies:
  flutter:
    sdk: flutter
  shared_preferences: ^2.2.2
```

---

# 2️⃣ lib/main.dart

```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() {
  runApp(const SOSClimaApp());
}

class PedidoAjuda {
  String nome;
  String telefone;
  String bairro;
  String tipoAjuda;
  String descricao;
  int pessoas;
  bool riscoSaude;
  String status;

  PedidoAjuda({
    required this.nome,
    required this.telefone,
    required this.bairro,
    required this.tipoAjuda,
    required this.descricao,
    required this.pessoas,
    required this.riscoSaude,
    this.status = "Pendente",
  });
}

List<PedidoAjuda> pedidos = [];

class SOSClimaApp extends StatelessWidget {
  const SOSClimaApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'SOS Clima',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const VerificacaoTela(),
    );
  }
}

class VerificacaoTela extends StatefulWidget {
  const VerificacaoTela({super.key});

  @override
  State<VerificacaoTela> createState() => _VerificacaoTelaState();
}

class _VerificacaoTelaState extends State<VerificacaoTela> {

  @override
  void initState() {
    super.initState();
    verificarTermos();
  }

  Future<void> verificarTermos() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();

    bool aceitou = prefs.getBool('aceitouTermos') ?? false;

    if (aceitou) {
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(builder: (_) => const HomeScreen()),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    return const TermosScreen();
  }
}

class TermosScreen extends StatelessWidget {
  const TermosScreen({super.key});

  Future<void> aceitarTermos(BuildContext context) async {
    SharedPreferences prefs = await SharedPreferences.getInstance();

    await prefs.setBool('aceitouTermos', true);

    Navigator.pushReplacement(
      context,
      MaterialPageRoute(builder: (_) => const HomeScreen()),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Termos e Consentimento"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          children: [
            const Expanded(
              child: SingleChildScrollView(
                child: Text(
                  "O aplicativo SOS Clima conecta pessoas em situação "
                  "de vulnerabilidade com voluntários dispostos a ajudar "
                  "durante eventos climáticos extremos.",
                  style: TextStyle(fontSize: 18),
                ),
              ),
            ),
            ElevatedButton(
              onPressed: () => aceitarTermos(context),
              child: const Text("Concordo e Continuar"),
            ),
          ],
        ),
      ),
    );
  }
}

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  Widget botao(
      BuildContext context,
      String texto,
      Widget tela,
      IconData icone,
      ) {
    return Card(
      elevation: 4,
      child: ListTile(
        leading: Icon(icone),
        title: Text(texto),
        trailing: const Icon(Icons.arrow_forward),
        onTap: () {
          Navigator.push(
            context,
            MaterialPageRoute(builder: (_) => tela),
          );
        },
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("SOS Clima"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(12),
        child: Column(
          children: [
            botao(
              context,
              "Pedir Ajuda",
              const PedirAjudaScreen(),
              Icons.warning,
            ),
            botao(
              context,
              "Quero Ajudar",
              const QueroAjudarScreen(),
              Icons.volunteer_activism,
            ),
            botao(
              context,
              "Painel de Ocorrências",
              const OcorrenciasScreen(),
              Icons.list,
            ),
            botao(
              context,
              "Configurações",
              const ConfigScreen(),
              Icons.settings,
            ),
          ],
        ),
      ),
    );
  }
}

class PedirAjudaScreen extends StatefulWidget {
  const PedirAjudaScreen({super.key});

  @override
  State<PedirAjudaScreen> createState() => _PedirAjudaScreenState();
}

class _PedirAjudaScreenState extends State<PedirAjudaScreen> {

  final _formKey = GlobalKey<FormState>();

  final nomeController = TextEditingController();
  final telefoneController = TextEditingController();
  final bairroController = TextEditingController();
  final descricaoController = TextEditingController();
  final pessoasController = TextEditingController();

  String tipoAjuda = "Alimento";
  bool riscoSaude = false;

  void enviarPedido() {

    if (_formKey.currentState!.validate()) {

      PedidoAjuda pedido = PedidoAjuda(
        nome: nomeController.text,
        telefone: telefoneController.text,
        bairro: bairroController.text,
        tipoAjuda: tipoAjuda,
        descricao: descricaoController.text,
        pessoas: int.tryParse(pessoasController.text) ?? 0,
        riscoSaude: riscoSaude,
      );

      setState(() {
        pedidos.add(pedido);
      });

      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text("Pedido enviado com sucesso!"),
        ),
      );

      nomeController.clear();
      telefoneController.clear();
      bairroController.clear();
      descricaoController.clear();
      pessoasController.clear();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Pedir Ajuda"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Form(
          key: _formKey,
          child: ListView(
            children: [

              TextFormField(
                controller: nomeController,
                decoration: const InputDecoration(
                  labelText: "Nome",
                ),
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return "Campo obrigatório";
                  }
                  return null;
                },
              ),

              TextFormField(
                controller: telefoneController,
                decoration: const InputDecoration(
                  labelText: "Telefone",
                ),
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return "Campo obrigatório";
                  }
                  return null;
                },
              ),

              TextFormField(
                controller: bairroController,
                decoration: const InputDecoration(
                  labelText: "Bairro",
                ),
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return "Campo obrigatório";
                  }
                  return null;
                },
              ),

              DropdownButtonFormField(
                value: tipoAjuda,
                items: const [
                  DropdownMenuItem(
                    value: "Alimento",
                    child: Text("Alimento"),
                  ),
                  DropdownMenuItem(
                    value: "Água",
                    child: Text("Água"),
                  ),
                  DropdownMenuItem(
                    value: "Abrigo",
                    child: Text("Abrigo"),
                  ),
                ],
                onChanged: (value) {
                  setState(() {
                    tipoAjuda = value!;
                  });
                },
                decoration: const InputDecoration(
                  labelText: "Tipo de ajuda",
                ),
              ),

              TextFormField(
                controller: descricaoController,
                decoration: const InputDecoration(
                  labelText: "Descrição",
                ),
                maxLines: 3,
                validator: (value) {
                  if (value == null || value.length < 15) {
                    return "Mínimo de 15 caracteres";
                  }
                  return null;
                },
              ),

              TextFormField(
                controller: pessoasController,
                keyboardType: TextInputType.number,
                decoration: const InputDecoration(
                  labelText: "Quantidade de pessoas afetadas",
                ),
              ),

              SwitchListTile(
                title: const Text("Risco à saúde"),
                value: riscoSaude,
                onChanged: (value) {
                  setState(() {
                    riscoSaude = value;
                  });
                },
              ),

              const SizedBox(height: 20),

              ElevatedButton(
                onPressed: enviarPedido,
                child: const Text("Enviar Pedido"),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

class QueroAjudarScreen extends StatefulWidget {
  const QueroAjudarScreen({super.key});

  @override
  State<QueroAjudarScreen> createState() => _QueroAjudarScreenState();
}

class _QueroAjudarScreenState extends State<QueroAjudarScreen> {

  bool comida = false;
  bool transporte = false;
  bool primeirosSocorros = false;

  bool disponivelAgora = false;

  String transporteSelecionado = "Carro";

  final nomeController = TextEditingController();
  final telefoneController = TextEditingController();
  final bairroController = TextEditingController();

  void registrarOferta() {

    ScaffoldMessenger.of(context).showSnackBar(
      const SnackBar(
        content: Text("Oferta registrada com sucesso!"),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Quero Ajudar"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: ListView(
          children: [

            TextField(
              controller: nomeController,
              decoration: const InputDecoration(
                labelText: "Nome",
              ),
            ),

            TextField(
              controller: telefoneController,
              decoration: const InputDecoration(
                labelText: "Telefone",
              ),
            ),

            TextField(
              controller: bairroController,
              decoration: const InputDecoration(
                labelText: "Bairro de atuação",
              ),
            ),

            const SizedBox(height: 15),

            const Text(
              "Habilidades",
              style: TextStyle(
                fontSize: 18,
                fontWeight: FontWeight.bold,
              ),
            ),

            CheckboxListTile(
              title: const Text("Distribuição de comida"),
              value: comida,
              onChanged: (value) {
                setState(() {
                  comida = value!;
                });
              },
            ),

            CheckboxListTile(
              title: const Text("Transporte"),
              value: transporte,
              onChanged: (value) {
                setState(() {
                  transporte = value!;
                });
              },
            ),

            CheckboxListTile(
              title: const Text("Primeiros socorros"),
              value: primeirosSocorros,
              onChanged: (value) {
                setState(() {
                  primeirosSocorros = value!;
                });
              },
            ),

            DropdownButtonFormField(
              value: transporteSelecionado,
              items: const [
                DropdownMenuItem(
                  value: "Carro",
                  child: Text("Carro"),
                ),
                DropdownMenuItem(
                  value: "Moto",
                  child: Text("Moto"),
                ),
                DropdownMenuItem(
                  value: "Bicicleta",
                  child: Text("Bicicleta"),
                ),
              ],
              onChanged: (value) {
                setState(() {
                  transporteSelecionado = value!;
                });
              },
              decoration: const InputDecoration(
                labelText: "Meio de transporte",
              ),
            ),

            SwitchListTile(
              title: const Text("Disponível agora?"),
              value: disponivelAgora,
              onChanged: (value) {
                setState(() {
                  disponivelAgora = value;
                });
              },
            ),

            ElevatedButton(
              onPressed: registrarOferta,
              child: const Text("Registrar Oferta"),
            ),
          ],
        ),
      ),
    );
  }
}

class OcorrenciasScreen extends StatefulWidget {
  const OcorrenciasScreen({super.key});

  @override
  State<OcorrenciasScreen> createState() => _OcorrenciasScreenState();
}

class _OcorrenciasScreenState extends State<OcorrenciasScreen> {

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Painel de Ocorrências"),
      ),
      body: pedidos.isEmpty
          ? const Center(
        child: Text("Nenhuma ocorrência cadastrada"),
      )
          : ListView.builder(
        itemCount: pedidos.length,
        itemBuilder: (context, index) {

          PedidoAjuda pedido = pedidos[index];

          return Card(
            child: ListTile(
              title: Text(pedido.tipoAjuda),
              subtitle: Text(pedido.bairro),
              trailing: Text(pedido.status),
              onTap: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(
                    builder: (_) => DetalheScreen(
                      pedido: pedido,
                    ),
                  ),
                ).then((_) {
                  setState(() {});
                });
              },
            ),
          );
        },
      ),
    );
  }
}

class DetalheScreen extends StatefulWidget {

  final PedidoAjuda pedido;

  const DetalheScreen({
    super.key,
    required this.pedido,
  });

  @override
  State<DetalheScreen> createState() => _DetalheScreenState();
}

class _DetalheScreenState extends State<DetalheScreen> {

  void assumirAtendimento() {
    setState(() {
      widget.pedido.status = "Em atendimento";
    });
  }

  void concluirAtendimento() {
    setState(() {
      widget.pedido.status = "Concluído";
    });
  }

  @override
  Widget build(BuildContext context) {

    PedidoAjuda pedido = widget.pedido;

    return Scaffold(
      appBar: AppBar(
        title: const Text("Detalhes"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: ListView(
          children: [

            Text("Nome: ${pedido.nome}"),
            Text("Telefone: ${pedido.telefone}"),
            Text("Bairro: ${pedido.bairro}"),
            Text("Tipo de ajuda: ${pedido.tipoAjuda}"),
            Text("Descrição: ${pedido.descricao}"),
            Text("Pessoas afetadas: ${pedido.pessoas}"),
            Text("Risco à saúde: ${pedido.riscoSaude ? "Sim" : "Não"}"),
            Text("Status: ${pedido.status}"),

            const SizedBox(height: 20),

            ElevatedButton(
              onPressed: assumirAtendimento,
              child: const Text("Assumir Atendimento"),
            ),

            ElevatedButton(
              onPressed: concluirAtendimento,
              child: const Text("Concluir"),
            ),
          ],
        ),
      ),
    );
  }
}

class ConfigScreen extends StatelessWidget {
  const ConfigScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Configurações"),
      ),
      body: const Center(
        child: Text("Tela de configurações"),
      ),
    );
  }
}
```
