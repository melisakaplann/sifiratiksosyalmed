import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';
import 'dart:async';
import 'dart:io';
import 'package:image_picker/image_picker.dart';
import 'package:intl/intl.dart';
import 'package:flutter_email_sender/flutter_email_sender.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Giriş Uygulaması',
      home: WelcomePage(),
    );
  }
}

class WelcomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    Timer(Duration(seconds: 2), () {
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(builder: (context) => AuthPage()),
      );
    });

    return Scaffold(
      body: Stack(
        children: [
          Container(
            decoration: BoxDecoration(
              image: DecorationImage(
                image: NetworkImage(
                    'https://cdn.pixabay.com/photo/2022/07/26/09/28/background-7345432_1280.jpg'),
                fit: BoxFit.cover,
                colorFilter: ColorFilter.mode(
                  Colors.black.withOpacity(0.5),
                  BlendMode.darken,
                ),
              ),
            ),
          ),
          Center(
            child: Text(
              'Hoş Geldiniz',
              style: TextStyle(
                fontSize: 48,
                fontWeight: FontWeight.bold,
                color: Colors.white,
                shadows: [
                  Shadow(
                    blurRadius: 10.0,
                    color: Colors.black,
                    offset: Offset(5.0, 5.0),
                  ),
                ],
              ),
            ),
          ),
        ],
      ),
    );
  }
}

class AuthPage extends StatefulWidget {
  @override
  _AuthPageState createState() => _AuthPageState();
}

class _AuthPageState extends State<AuthPage> {
  final TextEditingController emailController = TextEditingController();
  final TextEditingController passwordController = TextEditingController();
  bool _isPasswordVisible = false;

  void _handleRegister() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    await prefs.setString('email', emailController.text);
    await prefs.setString('password', passwordController.text);

    ScaffoldMessenger.of(context).showSnackBar(SnackBar(
      content: Text('Kayıt başarılı!'),
    ));
  }

  void _handleLogin() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    String? storedEmail = prefs.getString('email');
    String? storedPassword = prefs.getString('password');

    if (storedEmail != null &&
        storedPassword != null &&
        emailController.text == storedEmail &&
        passwordController.text == storedPassword) {
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text('Hoş geldin, ${emailController.text}!'),
      ));
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(builder: (context) => HomeScreen()),
      );
    } else {
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text('Geçersiz e-posta veya şifre.'),
      ));
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Kayıt Ol / Giriş Yap')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            TextField(
              controller: emailController,
              decoration: InputDecoration(
                labelText: 'E-posta',
                border: OutlineInputBorder(),
                prefixIcon: Icon(Icons.email),
              ),
            ),
            SizedBox(height: 20),
            TextField(
              controller: passwordController,
              obscureText: !_isPasswordVisible,
              decoration: InputDecoration(
                labelText: 'Şifre',
                border: OutlineInputBorder(),
                prefixIcon: Icon(Icons.lock),
                suffixIcon: IconButton(
                  icon: Icon(_isPasswordVisible
                      ? Icons.visibility
                      : Icons.visibility_off),
                  onPressed: () {
                    setState(() {
                      _isPasswordVisible = !_isPasswordVisible;
                    });
                  },
                ),
              ),
            ),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: _handleRegister,
              child: Text('Kayıt Ol'),
            ),
            ElevatedButton(
              onPressed: _handleLogin,
              child: Text('Giriş Yap'),
            ),
          ],
        ),
      ),
    );
  }
}

class HomeScreen extends StatelessWidget {
  const HomeScreen({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Ana Sayfa'),
        leading: Builder(
          builder: (BuildContext context) {
            return IconButton(
              icon: const Icon(Icons.menu),
              onPressed: () {
                Scaffold.of(context).openDrawer();
              },
            );
          },
        ),
      ),
      drawer: Drawer(
        child: ListView(
          padding: EdgeInsets.zero,
          children: <Widget>[
            DrawerHeader(
              decoration: const BoxDecoration(color: Color(0xff659567)),
              child: const Text(
                'Menü',
                style: TextStyle(color: Color(0xffe5cdc5), fontSize: 24),
              ),
            ),
            ListTile(
              title: const Text('Ayarlar',
                  style: TextStyle(color: Color(0xff112911))),
              onTap: () {
                Navigator.push(context,
                    MaterialPageRoute(builder: (context) => SettingsPage()));
              },
            ),
            ListTile(
              title: const Text('Profil',
                  style: TextStyle(color: Color(0xff112911))),
              onTap: () {
                Navigator.push(
                    context,
                    MaterialPageRoute(
                        builder: (context) => const ProfilePage()));
              },
            ),
            ListTile(
              title: const Text('Arkadaşlar',
                  style: TextStyle(color: Color(0xff112911))),
              onTap: () {
                Navigator.push(
                    context,
                    MaterialPageRoute(
                        builder: (context) => const FriendsPage()));
              },
            ),
            ListTile(
              title: const Text('Oyun',
                  style: TextStyle(color: Color(0xff112911))),
              onTap: () {
                Navigator.push(context,
                    MaterialPageRoute(builder: (context) => const GamesPage()));
              },
            ),
            ListTile(
              title: const Text('Etkinlik',
                  style: TextStyle(color: Color(0xff112911))),
              onTap: () {
                Navigator.push(
                    context,
                    MaterialPageRoute(
                        builder: (context) => const EventsPage()));
              },
            ),
            ListTile(
              title: const Text('Ajanda',
                  style: TextStyle(color: Color(0xff112911))),
              onTap: () {
                Navigator.push(
                    context,
                    MaterialPageRoute(
                        builder: (context) => const CalendarPage()));
              },
            ),
          ],
        ),
      ),
      body: Container(
        decoration: BoxDecoration(
          image: DecorationImage(
            image: const NetworkImage(
              'https://png.pngtree.com/thumb_back/fh260/background/20240715/pngtree-painting-of-soft-green-and-gold-leaves-image_15866262.jpg',
            ),
            fit: BoxFit.cover,
            colorFilter: ColorFilter.mode(
                Colors.black.withOpacity(0.5), BlendMode.darken),
          ),
        ),
        child: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              const Text(
                'Hoş geldiniz!',
                style: TextStyle(
                    fontSize: 32,
                    color: Colors.white,
                    fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 20),
              ElevatedButton(
                onPressed: () {
                  Navigator.push(
                      context,
                      MaterialPageRoute(
                          builder: (context) => const TaskListPage()));
                },
                child: const Text('Görev Listesi'),
              ),
              ElevatedButton(
                onPressed: () {
                  Navigator.push(
                      context,
                      MaterialPageRoute(
                          builder: (context) => const BlogPage()));
                },
                child: const Text('Blog Yazıları'),
              ),
              ElevatedButton(
                onPressed: () {
                  Navigator.push(
                    context,
                    MaterialPageRoute(
                        builder: (context) => DIYPage()), // const kaldırıldı
                  );
                },
                child: const Text(
                    'Kendin Yap Projeleri'), // child parametresi eklendi
              ),
              ElevatedButton(
                onPressed: () {
                  Navigator.push(
                    context,
                    MaterialPageRoute(
                        builder: (context) => GoalsPage()), // const kaldırıldı
                  );
                },
                child: const Text('Hedefler'),
              ),
              ElevatedButton(
                onPressed: () {
                  Navigator.push(
                      context,
                      MaterialPageRoute(
                          builder: (context) => const ContactPage()));
                },
                child: const Text('İletişim'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

class SettingsPage extends StatefulWidget {
  @override
  _SettingsPageState createState() => _SettingsPageState();
}

class _SettingsPageState extends State<SettingsPage> {
  final TextEditingController _newPasswordController = TextEditingController();
  final TextEditingController _confirmPasswordController =
      TextEditingController();
  String? _storedPassword;

  @override
  void initState() {
    super.initState();
    _loadPassword();
  }

  Future<void> _loadPassword() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _storedPassword = prefs.getString('password');
    });
  }

  Future<void> _changePassword() async {
    if (_newPasswordController.text == _confirmPasswordController.text) {
      SharedPreferences prefs = await SharedPreferences.getInstance();
      await prefs.setString('password', _newPasswordController.text);
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text('Şifre başarıyla değiştirildi!'),
      ));
    } else {
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text('Yeni şifreler uyuşmuyor.'),
      ));
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Ayarlar')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            Text(
              'Mevcut Şifre: ${_storedPassword ?? "Henüz ayarlanmadı"}',
              style: TextStyle(fontSize: 16),
            ),
            SizedBox(height: 20),
            TextField(
              controller: _newPasswordController,
              obscureText: true,
              decoration: InputDecoration(
                labelText: 'Yeni Şifre',
                border: OutlineInputBorder(),
              ),
            ),
            SizedBox(height: 20),
            TextField(
              controller: _confirmPasswordController,
              obscureText: true,
              decoration: InputDecoration(
                labelText: 'Yeni Şifreyi Onayla',
                border: OutlineInputBorder(),
              ),
            ),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: _changePassword,
              child: Text('Şifreyi Değiştir'),
            ),
          ],
        ),
      ),
    );
  }
}

class ProfilePage extends StatefulWidget {
  const ProfilePage({Key? key}) : super(key: key);

  @override
  _ProfilePageState createState() => _ProfilePageState();
}

class _ProfilePageState extends State<ProfilePage> {
  String email = '';
  String biography = '';
  String? profileImagePath;
  final TextEditingController bioController = TextEditingController();

  @override
  void initState() {
    super.initState();
    _loadProfile();
  }

  _loadProfile() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      email = prefs.getString('email') ?? '';
      biography = prefs.getString('biography') ?? '';
      profileImagePath = prefs.getString('profileImagePath');
      bioController.text = biography;
    });
  }

  _saveProfile() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    await prefs.setString('biography', bioController.text);
    await prefs.setString('profileImagePath', profileImagePath ?? '');
    ScaffoldMessenger.of(context).showSnackBar(SnackBar(
      content: Text('Profil kaydedildi!'),
    ));
  }

  Future<void> _pickImage() async {
    final picker = ImagePicker();
    final pickedFile = await picker.getImage(source: ImageSource.gallery);

    if (pickedFile != null) {
      setState(() {
        profileImagePath = pickedFile.path;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Profil')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            GestureDetector(
              onTap: _pickImage,
              child: CircleAvatar(
                radius: 50,
                backgroundImage: profileImagePath != null
                    ? FileImage(File(profileImagePath!))
                    : null,
                child: profileImagePath == null
                    ? Icon(Icons.camera_alt, size: 50)
                    : null,
              ),
            ),
            const SizedBox(height: 20),
            Text(
              'E-posta: $email',
              style: TextStyle(fontSize: 18),
            ),
            const SizedBox(height: 20),
            TextField(
              controller: bioController,
              decoration: InputDecoration(
                labelText: 'Biyografi',
                border: OutlineInputBorder(),
              ),
              maxLines: 4,
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: _saveProfile,
              child: const Text('Kaydet'),
            ),
          ],
        ),
      ),
    );
  }
}

class FriendsPage extends StatefulWidget {
  const FriendsPage({Key? key}) : super(key: key);

  @override
  _FriendsPageState createState() => _FriendsPageState();
}

class _FriendsPageState extends State<FriendsPage> {
  // Örnek arkadaş listesi
  final List<Map<String, String>> friends = [
    {'name': 'Ahmet Yılmaz', 'email': 'ahmet@example.com'},
    {'name': 'Ayşe Kaya', 'email': 'ayse@example.com'},
    {'name': 'Mehmet Demir', 'email': 'mehmet@example.com'},
    {'name': 'Zeynep Çelik', 'email': 'zeynep@example.com'},
  ];

  // Arkadaş ekleme
  void _addFriend(String name, String email) {
    setState(() {
      friends.add({'name': name, 'email': email});
    });
  }

  // Arkadaş eklemek için bir dialog
  Future<void> _showAddFriendDialog() async {
    String newName = '';
    String newEmail = '';

    return showDialog<void>(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          title: const Text('Yeni Arkadaş Ekle'),
          content: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              TextField(
                decoration: const InputDecoration(labelText: 'İsim'),
                onChanged: (value) {
                  newName = value;
                },
              ),
              TextField(
                decoration: const InputDecoration(labelText: 'E-posta'),
                onChanged: (value) {
                  newEmail = value;
                },
              ),
            ],
          ),
          actions: <Widget>[
            TextButton(
              child: const Text('İptal'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
            ElevatedButton(
              child: const Text('Ekle'),
              onPressed: () {
                if (newName.isNotEmpty && newEmail.isNotEmpty) {
                  _addFriend(newName, newEmail);
                  Navigator.of(context).pop();
                }
              },
            ),
          ],
        );
      },
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Arkadaşlar'),
        actions: [
          IconButton(
            icon: const Icon(Icons.person_add),
            onPressed: _showAddFriendDialog, // Yeni arkadaş ekleme
          ),
        ],
      ),
      body: ListView.builder(
        itemCount: friends.length,
        itemBuilder: (context, index) {
          return ListTile(
            title: Text(friends[index]['name']!),
            subtitle: Text(friends[index]['email']!),
            leading: const CircleAvatar(child: Icon(Icons.person)),
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) =>
                      FriendProfilePage(friend: friends[index]),
                ),
              );
            },
          );
        },
      ),
    );
  }
}

class FriendProfilePage extends StatelessWidget {
  final Map<String, String> friend;

  const FriendProfilePage({Key? key, required this.friend}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(friend['name']!)),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              'İsim: ${friend['name']}',
              style: const TextStyle(fontSize: 24),
            ),
            const SizedBox(height: 20),
            Text(
              'E-posta: ${friend['email']}',
              style: const TextStyle(fontSize: 18),
            ),
          ],
        ),
      ),
    );
  }
}

class GamesPage extends StatefulWidget {
  const GamesPage({Key? key}) : super(key: key);

  @override
  _GamesPageState createState() => _GamesPageState();
}

class _GamesPageState extends State<GamesPage>
    with SingleTickerProviderStateMixin {
  int resources = 100; // Başlangıç kaynak miktarı
  int pollution = 0; // Kirlilik seviyesi
  late Timer _timer;
  late AnimationController _animationController;

  @override
  void initState() {
    super.initState();
    _animationController = AnimationController(
      vsync: this,
      duration: const Duration(seconds: 2),
    );
    startSimulation();
  }

  @override
  void dispose() {
    _timer.cancel();
    _animationController.dispose();
    super.dispose();
  }

  void startSimulation() {
    _timer = Timer.periodic(const Duration(seconds: 2), (timer) {
      setState(() {
        if (resources > 0 && pollution < 100) {
          resources -= 5; // Kaynaklar azalıyor
          pollution += 5; // Kirlilik artıyor
        }
      });
    });
  }

  void recycle() {
    setState(() {
      if (pollution >= 10) {
        pollution -= 10; // Geri dönüşüm kirliliği azaltıyor
        resources += 10; // Kaynakları geri kazanıyor
        _animationController.forward(from: 0); // Animasyonu başlat
      }
    });
  }

  void plantTrees() {
    setState(() {
      if (resources >= 10) {
        pollution -= 15; // Ağaç dikmek kirliliği azaltıyor
        resources -= 10; // Kaynak harcanıyor
        _animationController.forward(from: 0); // Ağaç dikme animasyonu
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    Color backgroundColor = resources > 50
        ? Colors.green.shade200
        : Colors.brown.shade400; // Kaynaklar azalınca arka plan koyulaşıyor

    return Scaffold(
      appBar: AppBar(
        title: const Text('Sürdürülebilirlik Simülasyonu'),
      ),
      body: AnimatedContainer(
        duration: const Duration(milliseconds: 500),
        color: backgroundColor,
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            // Kaynak ve kirlilik göstergeleri
            Text(
              'Kaynaklar: $resources',
              style: const TextStyle(fontSize: 24),
            ),
            const SizedBox(height: 20),
            Text(
              'Kirlilik: $pollution',
              style: const TextStyle(fontSize: 24),
            ),
            const SizedBox(height: 40),

            // Geri Dönüşüm ve Ağaç Dikme Butonları
            ElevatedButton(
              onPressed: recycle,
              child: const Text('Geri Dönüşüm Yap'),
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: plantTrees,
              child: const Text('Ağaç Dik'),
            ),

            const SizedBox(height: 20),
            // Kirlilik ve kaynak durumu animasyonu
            resources <= 0 || pollution >= 100
                ? const Text(
                    'Oyun Bitti! Kaynaklar tükendi veya kirlilik çok arttı!',
                    style: TextStyle(
                        fontSize: 18,
                        fontWeight: FontWeight.bold,
                        color: Colors.red),
                  )
                : FadeTransition(
                    opacity: _animationController,
                    child: resources > 0
                        ? const Icon(
                            Icons.eco,
                            size: 50,
                            color: Colors.green,
                          )
                        : const Icon(
                            Icons.smoke_free,
                            size: 50,
                            color: Colors.grey,
                          ),
                  ),
          ],
        ),
      ),
    );
  }
}

class EventsPage extends StatefulWidget {
  const EventsPage({Key? key}) : super(key: key);

  @override
  _EventsPageState createState() => _EventsPageState();
}

class _EventsPageState extends State<EventsPage> {
  final List<Map<String, String>> _events = [];
  final TextEditingController _eventController = TextEditingController();
  DateTime? _selectedDate;
  TimeOfDay? _selectedTime;

  void _addEvent() {
    if (_eventController.text.isNotEmpty &&
        _selectedDate != null &&
        _selectedTime != null) {
      setState(() {
        _events.add({
          'title': _eventController.text,
          'date': DateFormat('dd/MM/yyyy').format(_selectedDate!),
          'time': _selectedTime!.format(context),
        });
        _eventController.clear();
        _selectedDate = null;
        _selectedTime = null;
      });
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text('Etkinlik eklendi!'),
      ));
    } else {
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text('Lütfen tüm alanları doldurun.'),
      ));
    }
  }

  void _removeEvent(int index) {
    setState(() {
      _events.removeAt(index);
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text('Etkinlik silindi!'),
      ));
    });
  }

  Future<void> _selectDate(BuildContext context) async {
    final DateTime? picked = await showDatePicker(
      context: context,
      initialDate: _selectedDate ?? DateTime.now(),
      firstDate: DateTime(2000),
      lastDate: DateTime(2101),
    );
    if (picked != null && picked != _selectedDate) {
      setState(() {
        _selectedDate = picked;
      });
    }
  }

  Future<void> _selectTime(BuildContext context) async {
    final TimeOfDay? picked = await showTimePicker(
      context: context,
      initialTime: _selectedTime ?? TimeOfDay.now(),
    );
    if (picked != null && picked != _selectedTime) {
      setState(() {
        _selectedTime = picked;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Etkinlikler')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _eventController,
              decoration: InputDecoration(
                labelText: 'Yeni Etkinlik',
                border: OutlineInputBorder(),
              ),
            ),
            SizedBox(height: 10),
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceBetween,
              children: [
                ElevatedButton(
                  onPressed: () => _selectDate(context),
                  child: Text(_selectedDate == null
                      ? 'Tarih Seç'
                      : 'Seçilen Tarih: ${DateFormat('dd/MM/yyyy').format(_selectedDate!)}'),
                ),
                ElevatedButton(
                  onPressed: () => _selectTime(context),
                  child: Text(_selectedTime == null
                      ? 'Saat Seç'
                      : 'Seçilen Saat: ${_selectedTime!.format(context)}'),
                ),
              ],
            ),
            SizedBox(height: 10),
            ElevatedButton(
              onPressed: _addEvent,
              child: Text('Etkinliği Ekle'),
            ),
            SizedBox(height: 20),
            Expanded(
              child: ListView.builder(
                itemCount: _events.length,
                itemBuilder: (context, index) {
                  return ListTile(
                    title: Text(_events[index]['title']!),
                    subtitle: Text(
                        'Tarih: ${_events[index]['date']}, Saat: ${_events[index]['time']}'),
                    trailing: IconButton(
                      icon: Icon(Icons.delete),
                      onPressed: () => _removeEvent(index),
                    ),
                  );
                },
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class CalendarPage extends StatefulWidget {
  const CalendarPage({Key? key}) : super(key: key);

  @override
  _CalendarPageState createState() => _CalendarPageState();
}

class _CalendarPageState extends State<CalendarPage> {
  final List<String> _events = [];
  final TextEditingController _eventController = TextEditingController();

  void _addEvent() {
    if (_eventController.text.isNotEmpty) {
      setState(() {
        _events.add(_eventController.text);
        _eventController.clear();
      });
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text('Etkinlik eklendi!'),
      ));
    }
  }

  void _removeEvent(int index) {
    setState(() {
      _events.removeAt(index);
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text('Etkinlik silindi!'),
      ));
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Ajanda')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _eventController,
              decoration: InputDecoration(
                labelText: 'Yeni Etkinlik',
                border: OutlineInputBorder(),
              ),
            ),
            SizedBox(height: 10),
            ElevatedButton(
              onPressed: _addEvent,
              child: Text('Etkinliği Ekle'),
            ),
            SizedBox(height: 20),
            Expanded(
              child: ListView.builder(
                itemCount: _events.length,
                itemBuilder: (context, index) {
                  return ListTile(
                    title: Text(_events[index]),
                    trailing: IconButton(
                      icon: Icon(Icons.delete),
                      onPressed: () => _removeEvent(index),
                    ),
                  );
                },
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class TaskListPage extends StatefulWidget {
  const TaskListPage({Key? key}) : super(key: key);

  @override
  _TaskListPageState createState() => _TaskListPageState();
}

class _TaskListPageState extends State<TaskListPage> {
  List<String> _tasks = [
    'Geri dönüşüm kutusunu kontrol et.',
    'Plastik poşet kullanma.',
    'Tekrar kullanılabilir su şişesi kullan.',
    'Atıkları ayrıştır.',
    'Biyolojik atıkları kompost yap.',
    'İkinci el eşya almayı düşün.',
    'Enerji tasarruflu ampuller kullan.',
    'İhtiyaç fazlası eşyaları bağışla.',
    'Günlük alımını listele ve gereksiz alışveriş yapma.',
    'Dijital belgeleri tercih et.'
  ];

  List<bool> _completedTasks = List.generate(10, (index) => false);

  @override
  void initState() {
    super.initState();
    _loadCompletedTasks();
  }

  Future<void> _loadCompletedTasks() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    List<String>? completed = prefs.getStringList('completedTasks');
    if (completed != null) {
      _completedTasks = completed.map((task) => task == 'true').toList();
      setState(() {});
    }
  }

  Future<void> _toggleTask(int index) async {
    setState(() {
      _completedTasks[index] = !_completedTasks[index];
    });
    SharedPreferences prefs = await SharedPreferences.getInstance();
    await prefs.setStringList(
        'completedTasks', _completedTasks.map((e) => e.toString()).toList());
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Görev Listesi')),
      body: ListView.builder(
        itemCount: _tasks.length,
        itemBuilder: (context, index) {
          return ListTile(
            title: Text(
              _tasks[index],
              style: TextStyle(
                decoration:
                    _completedTasks[index] ? TextDecoration.lineThrough : null,
              ),
            ),
            leading: Checkbox(
              value: _completedTasks[index],
              onChanged: (bool? value) {
                _toggleTask(index);
              },
            ),
          );
        },
      ),
    );
  }
}

class BlogPage extends StatefulWidget {
  const BlogPage({Key? key}) : super(key: key);

  @override
  _BlogPageState createState() => _BlogPageState();
}

class _BlogPageState extends State<BlogPage> {
  List<String> _blogPosts = [];
  final TextEditingController _postController = TextEditingController();

  @override
  void initState() {
    super.initState();
    _loadBlogPosts();
  }

  Future<void> _loadBlogPosts() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _blogPosts = prefs.getStringList('blogPosts') ?? [];
    });
  }

  Future<void> _addBlogPost() async {
    if (_postController.text.isNotEmpty) {
      SharedPreferences prefs = await SharedPreferences.getInstance();
      _blogPosts.add(_postController.text);
      await prefs.setStringList('blogPosts', _blogPosts);
      _postController.clear();
      _loadBlogPosts();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Blog Yazıları')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _postController,
              decoration: InputDecoration(
                labelText: 'Yeni Blog Yazısı',
                border: OutlineInputBorder(),
              ),
              maxLines: 3,
            ),
            SizedBox(height: 10),
            ElevatedButton(
              onPressed: _addBlogPost,
              child: const Text('Ekle'),
            ),
            SizedBox(height: 20),
            Expanded(
              child: ListView.builder(
                itemCount: _blogPosts.length,
                itemBuilder: (context, index) {
                  return Card(
                    child: Padding(
                      padding: const EdgeInsets.all(8.0),
                      child: Text(
                        _blogPosts[index],
                        style: const TextStyle(fontSize: 16),
                      ),
                    ),
                  );
                },
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class DIYPage extends StatelessWidget {
  final List<Map<String, String>> projects = [
    {
      'title': 'Pet Şişe Saksı',
      'description': 'Pet şişelerden saksı yapımı.',
      'image': 'assets/images/pet_sakisi.gif',
      'instructions':
          '1. Pet şişeyi kesin.\n2. İçine toprak ekleyin.\n3. Bitki dikin.'
    },
    // Diğer projeleri buraya ekleyin.
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Kendin Yap Projeleri')),
      body: ListView.builder(
        itemCount: projects.length,
        itemBuilder: (context, index) {
          return Card(
            margin: EdgeInsets.all(10),
            child: Column(
              children: [
                Image.asset(projects[index]['image']!),
                Padding(
                  padding: const EdgeInsets.all(10.0),
                  child: Text(
                    projects[index]['title']!,
                    style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
                  ),
                ),
                ElevatedButton(
                  onPressed: () {
                    showDialog(
                      context: context,
                      builder: (context) {
                        return AlertDialog(
                          title: Text(projects[index]['title']!),
                          content: Text(projects[index]['instructions']!),
                          actions: [
                            TextButton(
                              child: const Text('Kapat'),
                              onPressed: () {
                                Navigator.of(context).pop();
                              },
                            ),
                          ],
                        );
                      },
                    );
                  },
                  child: const Text('Nasıl Yapılır?'),
                ),
              ],
            ),
          );
        },
      ),
    );
  }
}

class ProjectDetailPage extends StatelessWidget {
  final Map<String, String> project;

  const ProjectDetailPage({Key? key, required this.project}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(project['title']!)),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Image.network(
              project['image']!,
              height: 200,
              fit: BoxFit.cover,
            ),
            const SizedBox(height: 10),
            Text(
              'Nasıl Yapılır:',
              style: const TextStyle(fontSize: 22, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 10),
            Text(
              project['description']!,
              style: const TextStyle(fontSize: 16),
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                // Gerekirse projeye dair detaylı talimatlar eklenebilir.
              },
              child: const Text('Detaylı Talimatlar'),
            ),
          ],
        ),
      ),
    );
  }
}

class GoalsPage extends StatefulWidget {
  const GoalsPage({Key? key}) : super(key: key);

  @override
  _GoalsPageState createState() => _GoalsPageState();
}

class _GoalsPageState extends State<GoalsPage> {
  final List<String> _goals = [];
  final TextEditingController _goalController = TextEditingController();

  @override
  void initState() {
    super.initState();
    _loadGoals();
  }

  Future<void> _loadGoals() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _goals.addAll(prefs.getStringList('goals') ?? []);
    });
  }

  Future<void> _saveGoals() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    await prefs.setStringList('goals', _goals);
  }

  void _addGoal() {
    if (_goalController.text.isNotEmpty) {
      setState(() {
        _goals.add(_goalController.text);
        _goalController.clear();
        _saveGoals();
      });
    }
  }

  void _toggleGoal(int index) {
    setState(() {
      if (_goals[index].endsWith(" ✔")) {
        _goals[index] = _goals[index].replaceAll(" ✔", "");
      } else {
        _goals[index] += " ✔"; // İşaretleme
      }
      _saveGoals();
    });
  }

  void _removeGoal(int index) {
    setState(() {
      _goals.removeAt(index);
      _saveGoals();
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Sıfır Atık Hedefleri'),
        actions: [
          IconButton(
            icon: const Icon(Icons.delete),
            onPressed: () {
              setState(() {
                _goals.clear();
                _saveGoals();
              });
            },
          )
        ],
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _goalController,
              decoration: InputDecoration(
                labelText: 'Yeni Hedef Ekle',
                border: OutlineInputBorder(),
                suffixIcon: IconButton(
                  icon: const Icon(Icons.add),
                  onPressed: _addGoal,
                ),
              ),
            ),
            const SizedBox(height: 20),
            Expanded(
              child: ListView.builder(
                itemCount: _goals.length,
                itemBuilder: (context, index) {
                  return Card(
                    margin: const EdgeInsets.symmetric(vertical: 8.0),
                    child: ListTile(
                      title: Text(
                        _goals[index],
                        style: TextStyle(
                          decoration: _goals[index].endsWith(" ✔")
                              ? TextDecoration.lineThrough
                              : TextDecoration.none,
                        ),
                      ),
                      trailing: IconButton(
                        icon: const Icon(Icons.delete, color: Colors.red),
                        onPressed: () => _removeGoal(index),
                      ),
                      onTap: () => _toggleGoal(index),
                    ),
                  );
                },
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class ContactPage extends StatelessWidget {
  const ContactPage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('İletişim')),
      body: const Center(child: ContactForm()),
    );
  }
}

class ContactForm extends StatefulWidget {
  const ContactForm({Key? key}) : super(key: key);

  @override
  _ContactFormState createState() => _ContactFormState();
}

class _ContactFormState extends State<ContactForm> {
  final TextEditingController _messageController = TextEditingController();

  Future<void> _sendEmail() async {
    final Email email = Email(
      body: _messageController.text,
      subject: 'İletişim',
      recipients: ['example@example.com'], // Burayı değiştirmeyi unutmayın
      isHTML: false,
    );

    await FlutterEmailSender.send(email);
    ScaffoldMessenger.of(context).showSnackBar(SnackBar(
      content: Text('Email gönderildi!'),
    ));
  }

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Column(
        children: [
          TextField(
            controller: _messageController,
            decoration: InputDecoration(
              labelText: 'Mesajınızı yazın',
              border: OutlineInputBorder(),
            ),
            maxLines: 4,
          ),
          const SizedBox(height: 20),
          ElevatedButton(
            onPressed: _sendEmail,
            child: const Text('Email Gönder'),
          ),
        ],
      ),
    );
  }
}
