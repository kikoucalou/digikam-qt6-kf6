# Digikam Qt6 + KF6 sous Linux Mint 22.2

Ce tutoriel explique comment compiler et configurer Digikam **master** avec **Qt6** et **KF6** dans un environnement propre, entièrement dans votre `$HOME`.

---

## 1️⃣ Pré-requis

Assurez-vous d'avoir installé les outils de compilation de base :

```bash
sudo apt update
sudo apt install build-essential cmake ninja-build git pkg-config gettext \
libglib2.0-dev libmysqlclient-dev libexiv2-dev
⚠️ Nous n'utilisons pas les Qt6/KF6 du système, mais des versions compilées dans $HOME.

2️⃣ Préparer un Qt6 propre
Téléchargez les sources de Qt6 et compilez-les dans $HOME/Qt/6.10 (ou version récente) :
cd $HOME
git clone <qt6_source_repo> Qt/6.10
cd Qt/6.10
./configure -prefix $HOME/Qt/6.10 -opensource -nomake examples -nomake tests
make -j16
make install

3️⃣ Préparer KDE Frameworks 6 (KF6)
Créez un dossier pour les sources KF6 et pour l'installation :
mkdir -p $HOME/kde/src
mkdir -p $HOME/kde/build
mkdir -p $HOME/kde/usr
Éditez un fichier YAML kde-builder.yml pour définir :
global:
  branch-group: kf6-qt6
  source-dir: ~/kde/src
  build-dir: ~/kde/build
  install-dir: ~/kde/usr
  log-dir: ~/kde/log
  cmake-generator: Ninja
  num-cores: 16
  ninja-options: "-j16"

cmake-options: >
  -DCMAKE_BUILD_TYPE=RelWithDebInfo
  -DBUILD_TESTING=OFF
  -DBUILD_PYTHON_BINDINGS=OFF
  -DBUILD_WITH_QT6=ON
  -DBUILD_WITH_QT5=OFF
  -DCMAKE_PREFIX_PATH=/home/USER/kde/usr:/home/USER/Qt/6.10
  -DKDE_INSTALL_PLUGINDIR=lib/digikam/plugins

set-env:
  PATH: "/home/USER/kde/usr/bin:/home/USER/Qt/6.10/bin:${PATH}"
  LD_LIBRARY_PATH: "/home/USER/kde/usr/lib:/home/USER/kde/usr/lib/x86_64-linux-gnu:/home/USER/Qt/6.10/lib:${LD_LIBRARY_PATH}"
  PKG_CONFIG_PATH: "/home/USER/kde/usr/lib/x86_64-linux-gnu/pkgconfig:/home/USER/kde/usr/lib/pkgconfig:/home/USER/Qt/6.10/lib/pkgconfig:${PKG_CONFIG_PATH}"
  CMAKE_PREFIX_PATH: "/home/USER/kde/usr:/home/USER/Qt/6.10"
  QT_PLUGIN_PATH: "/home/USER/Qt/6.10/plugins"
  DK_PLUGIN_PATH: "/home/USER/kde/usr/lib/digikam/plugins"
Remplacez USER par votre nom d'utilisateur.

4️⃣ Compiler KF6
Avec kde-builder :
cd $HOME/kde
kde-builder extra-cmake-modules
kde-builder kcoreaddons
kde-builder kwidgetsaddons
kde-builder kconfig
kde-builder kdbusaddons
kde-builder breeze-icons
kde-builder kiconthemes
kde-builder kcrash
kde-builder kitemmodels
kde-builder karchive
kde-builder kcolorscheme
kde-builder kguiaddons
Chaque projet doit être compilé avec Qt6.

5️⃣ Compiler Digikam
kde-builder digikam
⚠️ Assurez-vous que DK_PLUGIN_PATH est bien défini pour que Digikam charge ses plugins.

6️⃣ Lancer Digikam avec Qt6 et KF6
Créez un lanceur ou utilisez le terminal :
export DK_PLUGIN_PATH=$HOME/kde/usr/lib/digikam/plugins
export PATH=$HOME/Qt/6.10/bin:$HOME/kde/usr/bin:$PATH
export LD_LIBRARY_PATH=$HOME/Qt/6.10/lib:$HOME/kde/usr/lib:$HOME/kde/usr/lib/x86_64-linux-gnu
QT_LOGGING_RULES="digikam.general=true" $HOME/kde/usr/bin/digikam

7️⃣ Conseils
    • Vérifiez que Digikam utilise bien Qt6 :
$HOME/kde/usr/bin/digikam --version
    • Si les plugins ne se chargent pas, assurez-vous que DK_PLUGIN_PATH pointe vers le bon répertoire.
    • Nettoyer un build :
rm -rf $HOME/kde/build/*
    • Variables importantes pour Qt6 :
export CMAKE_PREFIX_PATH=$HOME/Qt/6.10:$HOME/kde/usr
export QT_PLUGIN_PATH=$HOME/Qt/6.10/plugins

8️⃣ Remarques
    • Ce tutoriel installe Qt6 et KF6 uniquement dans votre $HOME, aucun conflit avec le système.
    • Adapté pour Linux Mint 22.2, mais les chemins peuvent être modifiés pour d’autres distributions.
    • Testé avec Digikam master et Qt6.10 + KF6.21.

9️⃣ Références
    • Digikam Developer Docs
    • KDE Frameworks Building
    • Qt6 Documentation
