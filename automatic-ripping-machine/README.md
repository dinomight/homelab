# Automatic ripping machine

For device configuration in the `docker-compose.yml`, double check what device maps to which `/dev/sg*` device with this
command.
```bash
for sg in /dev/sg*; do
    echo -n "$sg → "; readlink -f /sys/class/scsi_generic/$(basename $sg)/device/block/* 2>/dev/null || echo "not a block device"
done
```