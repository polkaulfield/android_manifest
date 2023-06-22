# Kenvyra

An open-source Android ROM project with performance in mind.

Brought to you by the former DavinciCodeOS team.

## Preparation

Kenvyra uses SSH remotes, which means you will have to set up SSH access to GitHub and GitLab, please follow their documentation on how to do this: [GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) and [GitLab](https://docs.gitlab.com/ee/user/ssh.html). You'll have to have connected to each of them once to have them in yours known hosts file, otherwise syncing the manifest will error.

## Building

Building the ROM requires you to initialize the manifest first, like so:

```bash
mkdir kenvyra
cd kenvyra
repo init -u https://github.com/Kenvyra/android_manifest.git -b kenvyra-13.0
```

Then, make sure you add your device repositories to `.repo/local_manifests/local_manifest.xml`.

If you are building for an officially supported device, simply include the manifest for them, for example the Xiaomi SM6150 devices:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest>

  <include name="manifests/snippets/devices/xiaomi_sm6150.xml" />

</manifest>
```

For unofficial builds, you will have to manually add device trees and kernel here.

Now, download the sources:

```bash
THREAD_COUNT=$(nproc --all)
repo sync \
    -c \
    --jobs-network=$(( $THREAD_COUNT < 16 ? $THREAD_COUNT : 16 )) \
    -j$THREAD_COUNT \
    --jobs-checkout=$THREAD_COUNT \
    --force-sync \
    --no-clone-bundle \
    --no-tags
```

It's time to start building. We recommend using our container environment to ensure reproducibility for bug reports to us.

```bash
# Build the container image once (you don't need to run this every time, just once)
./build/scripts/build-image.sh

# For vanilla
./build/scripts/podman.sh "lunch kenvyra_device-buildtype; make bacon"
# For microG
./build/scripts/podman.sh "export KENVYRA_MICROG=true; lunch kenvyra_device-buildtype; make bacon"
# For GApps
./build/scripts/podman.sh "export KENVYRA_GAPPS=true; lunch kenvyra_device-buildtype; make bacon"
```

## Credits

- LineageOS team for the base
