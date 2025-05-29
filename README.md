# PlamettoRL
Running DRL frameworks with containers on Palmetto Cluster using headless mode.

## Downloading a container
1. Containers are processed as .sif file.
2. Container being used for coppeliaSim simulator is : docker://asalvi179/husky_cpsim:all
3. In a scratch directory (on a non-login node), run :
   
```
apptainer pull docker://asalvi179/husky_cpsim:all
```

## Running in batch mode
1. The following code

```
#!/bin/bash

#SBATCH --mem 250gb
#SBATCH --cpus-per-task 40
#SBATCH --time 50:00:00
#SBATCH --gpus 2


module purge
export PATH="/software/commercial/TurboVNC/bin/:$PATH"

password="aslf23r2"
echo -ne "${password}\n${spassword}" | vncpasswd -f > "vnc.passwd"

VNC_OUT=$(vncserver -log "vnc.log" -rfbauth "vnc.passwd"  -noxstartup  2>&1)

display=$(echo "${VNC_OUT}" | awk -F':' '/^Desktop/{print $NF}')
port=$((5900+display))

echo "Successfully started VNC server on $(hostname):${port}..."

export DISPLAY=:${display}
bash startxfce.sh &

#vglrun -d egl glxgears & 

#sleep 30

#Running the container
cd /scratch/asalvi/singularity_imgs/ &&
apptainer run --nv --env "DISPLAY=$DISPLAY" -B $HOME,$TMPDIR husky_cpsim_all.sif /home/asalvi/exec_32.sh &

#Pause for 30s
sleep 30

#Run the training
cd /scratch/asalvi/HuskyVS/train
module add anaconda3
source activate Husky_CS_SB3
python3 train_parallel.py --timesteps 2e7 > train_outputB.log 2>&1

```
