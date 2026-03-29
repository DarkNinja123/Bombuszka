# Bombuszka

Zestaw narzędzi do stress-testowania serwerów Minecraft. Zawiera kilka wariantów JAR, każdy z innymi możliwościami i silnikiem sieciowym.

---

## Bombka.jar — Wersja oryginalna (v1)

**Silnik:** MCProtocolLib (gotowa biblioteka protokołu Minecraft)
**Rozmiar:** 24 MB | **Data:** 29 marzec

Podstawowa wersja narzędzia mc-bots. Łączy wiele botów jednocześnie z serwerem Minecraft używając gotowej biblioteki do obsługi protokołu. Obsługuje proxy (SOCKS4/SOCKS5), generowanie nicków, opóźnienia logowania i minimalne tryby połączeń.

### Użycie

```
java -jar Bombka.jar -s <serwer[:port]> [opcje]
```

### Flagi

| Flaga | Pełna nazwa | Opis |
|---|---|---|
| `-s` | `--server` | Adres serwera IP[:port] **[WYMAGANE]** |
| `-c` | `--count` | Liczba botów |
| `-d` | `--delay` | Opóźnienie połączenia (ms) `<min> <max>` |
| `-p` | `--prefix` | Prefiks nicka bota |
| `-r` | `--real` | Generuj realistyczne nicki |
| `--nicks` | | Ścieżka do pliku z nickami (jeden nick na linię) |
| `-j` | `--join-msg` | Komendy/wiadomości po dołączeniu (oddzielone `&&`) |
| `-g` | `--gravity` | Symuluj grawitację (opadanie) |
| `-o` | `--online` | Tryb online (konto premium) |
| `-l` | `--proxy-list` | Ścieżka lub URL do listy proxy (`host:port` na linię) |
| `-t` | `--proxy-type` | Typ proxy: `SOCKS4` lub `SOCKS5` |
| `-ar` | `--auto-respawn` | Opóźnienie auto-respawnu (-1 aby wyłączyć) |
| `-m` | `--minimal` | Minimalny tryb bez listenerów |
| `-n` | `--nocolor` | Wyłącz kolorowanie wiadomości czatu |
| `-x` | `--most-minimal` | Najbardziej minimalny tryb — tylko połącz boty |

### Przykład

```
java -jar Bombka.jar -s mc.example.com:25565 -c 50 -r -l proxy.txt -t SOCKS5
```

---

## BombuszkaLepsza.jar — Wersja rozszerzona (v2.0)

**Silnik:** MCProtocolLib (fork z dodatkami)
**Rozmiar:** 23 MB | **Data:** 29 marzec

Ulepszona wersja Bombka.jar z trybem cichym (ograniczone spamowanie logów) i dwiema nowymi funkcjami niedostępnymi w v1.

### Nowe funkcje względem v1

| Flaga | Pełna nazwa | Opis |
|---|---|---|
| `-f` | `--follow` | Boty podążają za konkretnym graczem po dołączeniu |
| `-pass` | `--password` | Automatyczne logowanie/rejestracja przez AuthMe (`/login`, `/register`) |

Wszystkie pozostałe flagi identyczne jak w `Bombka.jar`.

### Użycie

```
java -jar BombuszkaLepsza.jar -s <serwer[:port]> [opcje]
```

### Przykład (serwer z AuthMe)

```
java -jar BombuszkaLepsza.jar -s mc.example.com -c 100 -r -pass mojehaslo123
```

---

## Bombuszka.jar — Wersja niestandardowa z modułem Proxy

**Silnik:** Netty (bezpośredni protokół TCP, bez MCProtocolLib)
**Rozmiar:** ~4.5 MB | **Data:** 29 marzec (kompilowana lokalnie)

Zbudowana na bazie projektu `minecraft-stress-test` z otwartym kodem źródłowym w katalogu `minecraft-stress-test/`. Zamiast gotowej biblioteki używa **bezpośredniej implementacji protokołu Minecraft przez Netty** — daje to niższe zużycie pamięci i wyższą skalowalność przy dużej liczbie botów. Dodany moduł Proxy integruje obsługę SOCKS4, SOCKS5 i HTTP przez wbudowane handlery Netty z mechanizmem round-robin.

### Protokół

Zaimplementowany protokół Minecraft **1.21.11** (numer wersji 774). Konfigurowalny przez właściwość `-Dbot.protocol.version`.

### Użycie

Sterowanie odbywa się przez właściwości JVM (`-D...`), a nie przez flagi CLI:

```
java [właściwości -D...] -jar Bombuszka.jar
```

### Właściwości JVM

| Właściwość | Opis | Domyślnie |
|---|---|---|
| `bot.ip` | Adres IP serwera | `127.0.0.1` |
| `bot.port` | Port serwera | `25565` |
| `bot.count` | Liczba botów | `1` |
| `bot.login.delay.ms` | Opóźnienie między kolejnymi logowaniami (ms) | `100` |
| `bot.name` | Bazowa nazwa botów (np. `Bot1`, `Bot2`, ...) | `Bot` |
| `bot.x` | Środkowa współrzędna X ruchu botów | `0` |
| `bot.z` | Środkowa współrzędna Z ruchu botów | `0` |
| `bot.logs` | Włącz/wyłącz logi (teleportacje itp.) | `true` |
| `bot.yaxis` | Ruch po osi Y (skoki/opadanie) | `true` |
| `bot.viewdistance` | Zasięg widzenia wysyłany do serwera | `2` |
| `bot.speed` | Prędkość ruchu botów | `0.1` |
| `bot.radius` | Promień ruchu wokół punktu środkowego | `1000` |
| `bot.proxy.file` | Ścieżka do pliku z proxy (`host:port` na linię) | — |
| `bot.proxy.type` | Typ proxy: `SOCKS4`, `SOCKS5` lub `HTTP` | `SOCKS5` |
| `bot.proxy.timeout.ms` | Timeout połączenia z proxy i handshake (ms) | `10000` |

### Komendy interaktywne (w czasie działania)

Po uruchomieniu programu można wpisywać komendy w konsoli:

| Komenda | Opis |
|---|---|
| `count <n>` | Zmień liczbę botów w czasie rzeczywistym |
| `speed <v>` | Zmień prędkość ruchu botów |
| `radius <v>` | Zmień promień ruchu |
| `logindelay <v>` | Zmień opóźnienie między logowaniami (ms) |
| `proxies` | Wyświetl wszystkie załadowane proxy |

### Format pliku proxy

Jeden wpis na linię w formacie `host:port`. Linie zaczynające się od `#` są komentarzami.

```
# Przykładowa lista proxy
103.37.82.134:1080
45.80.220.89:1080
192.168.1.100:1080
```

Boty otrzymują proxy w systemie **round-robin** (bot 1 → proxy 1, bot 2 → proxy 2 itd.).

### Przykłady

```bash
# Podstawowe uruchomienie (5 botów, bez proxy)
java -Dbot.ip=mc.example.com -Dbot.count=5 -jar Bombuszka.jar

# Pełne uruchomienie z proxy SOCKS4
java -Dbot.ip=mc.example.com -Dbot.count=1500 -Dbot.login.delay.ms=10 \
     -Dbot.proxy.file=proxy.txt -Dbot.proxy.type=SOCKS4 \
     -Dbot.proxy.timeout.ms=5000 \
     -jar Bombuszka.jar
```

---

## Porównanie narzędzi

| Cecha | Bombka.jar (v1) | BombuszkaLepsza.jar (v2) | Bombuszka.jar (custom) |
|---|---|---|---|
| Silnik | MCProtocolLib | MCProtocolLib | Netty (własny) |
| Proxy SOCKS4/5 | TAK | TAK | TAK |
| Proxy HTTP | NIE | NIE | TAK |
| Proxy timeout | NIE | NIE | TAK (konfigurowalny) |
| AuthMe (`-pass`) | NIE | TAK | NIE |
| Follow player (`-f`) | NIE | TAK | NIE |
| Komendy runtime | NIE | NIE | TAK |
| Protokół MC | automatyczny | automatyczny | 1.21.11 (konfigurowalny) |
