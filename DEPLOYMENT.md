# Notes for roll out.

## New servers

```
git clone https://github.com/SilphRoad/silph-i18n.git
for i in `ls -I=.git* -Inode_modules -I*.md -I package.json -I json`; do
  sudo -u www-data mkdir -p /srv/web/app/Locale/$i/LC_MESSAGES
  sudo -u www-data rsync -az $i/* /srv/web/app/Locale/$i/LC_MESSAGES/

  sudo -u www-data mkdir -p /srv/web/app/webroot/locale/$i/
  sudo -u www-data rsync -az json/$i/*.json /srv/web/app/webroot/locale/$i/
  sudo chown -R www-data.www-data /srv/web/app
done
```

## New text

```
POT=$(cd en_GB; ls *.po)
for i in `ls -I=.git* -Inode_modules -I*.md -I package.json -I json -I templates`; do
  for p in $POT; do
    pot2po -t $i/$p templates/${p}t $i/$p
  done
done
```

## JSON Generation

```
sudo apt install jq npm
sudo ln -s /usr/bin/nodejs /usr/bin/node
npm install

for i in `ls -I=.git* -Inode_modules -I*.md -I package.json -I json -I templates`; do
  mkdir -p json/$i
  echo $i
  node_modules/.bin/po2json -F --fallback-to-msgid -f mf $i/almanac_entry.po json/$i/almanac_entry.orig.json
  node_modules/.bin/po2json -F --fallback-to-msgid -f mf $i/nest_details.po json/$i/nest_details.orig.json
  node_modules/.bin/po2json -F --fallback-to-msgid -f mf $i/pokemon.po json/$i/pokemon.orig.json

  jq -s '.[0] * .[1]' json/$i/almanac_entry.orig.json json/$i/pokemon.orig.json > json/$i/almanac_entry.json
  jq -s '.[0] * .[1]' json/$i/nest_details.orig.json json/$i/pokemon.orig.json > json/$i/nest_details.json

  rm -f json/$i/*.orig.json
done
```

## New Domain

```
DOMAINS="text.po text2.po"
for i in `ls -I=.git* -Inode_modules -I*.md -I package.json -I json -I templates`; do
  for t in $DOMAINS; do
    cp templates/${t}t $i/$t
  done
done
```
