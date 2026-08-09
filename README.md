# Sipavo Desktop Distribution

Bu repository yalnız Sipavo Desktop dağıtım yüzeyidir. Web/backend kaynak kodu
ve Desktop uygulamasının source-of-truth'u burada tutulmaz.

## Sorumluluk

- macOS DMG/ZIP ve Windows NSIS artifact'leri
- `electron-updater` metadata dosyaları ve blockmap'ler
- `pilot` prerelease ve onaylı `stable` Desktop release'leri
- release güvenlik kapıları, rollback ve operasyon dokümantasyonu

## Kanallar

- `stable`: Production varsayılanıdır. Yalnız `X.Y.Z`, `latest-*` metadata ve
  açık `confirm=RELEASE` onayıyla yayınlanabilir.
- `pilot`: Açıkça `SIPAVO_DESKTOP_UPDATE_CHANNEL=pilot` kullanan prerelease
  build'ler içindir. Yalnız `X.Y.Z-pilot.N`, `pilot-*` metadata ve GitHub
  **Pre-release** olarak yayınlanır.

Stable Desktop `allowPrerelease=false` ve `latest` kanalını kullanır. Pilot
Desktop `allowPrerelease=true` ve `pilot` kanalını kullanır. Pilot metadata,
stable metadata'nın üzerine yazılmaz.

## Release workflow

`Desktop Distribution` workflow'u yalnız manuel çalışır. Kaynağı
`Legendejavu/sipova` içindeki açıkça verilen ref'ten checkout eder; bu repoya
uygulama kaynak kodu kopyalamaz. `publish=false` varsayılandır. Stable publish
için ayrıca `channel=stable`, semver stable sürüm ve `confirm=RELEASE` gerekir.
Pilot publish için `channel=pilot`, `-pilot.N` sürümü ve `confirm=PILOT` gerekir.

## Gerekli secrets

- `MAC_CSC_LINK`: Base64 PKCS#12 Developer ID identity
- `MAC_CSC_KEY_PASSWORD`: PKCS#12 parolası
- `APPLE_API_KEY`: Base64 App Store Connect `.p8`
- `APPLE_API_KEY_ID`: App Store Connect Key ID
- `APPLE_API_ISSUER`: App Store Connect Issuer ID
- `WIN_CSC_LINK`: Trusted CA organization-validated Windows code-signing PFX/P12
- `WIN_CSC_KEY_PASSWORD`: Windows signing export password
- `SOURCE_REPO_TOKEN`: `Legendejavu/sipova` için yalnız Contents: Read yetkili
  fine-grained token; classic veya geniş kapsamlı token kullanmayın

Secret değerleri repository dosyalarına veya release loglarına yazılmaz.

## Rollback ve acil durum

Problemli pilot prerelease silinmez; görünür biçimde geri çekilmiş olarak
işaretlenir ve daha yüksek bir `pilot.N` yayımlanır. Stable rollback bir
downgrade değildir: düzeltme daha yüksek stable semver ile, production onayı
sonrasında yayımlanır. Sertifika şüphesinde workflow durdurulur, ilgili GitHub
secrets ve Apple credential'ları rotate edilir.

Production release; signing, notarization, stapling, Gatekeeper, updater
metadata, migration ve gerçek cihaz kapıları geçmeden onaylanamaz.

Windows publish ayrıca aynı repository'deki temiz Windows acceptance run'ından
üretilmiş `sipavo-windows-migration-evidence` artifact'ının run ID'sini ister.
Serbest metin/secret içindeki JSON migration kanıtı olarak kabul edilmez.
