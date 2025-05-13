# Help-you-run-Lerobot-pi0-successfully
If you're unfamiliar with lerobot/pi0, this repo can help you get it up and running quickly.

Evaluate the ALOHA_transfer_cube task quickly.
In eval.py, add the following:

    from lerobot.common.policies.pi0.modeling_pi0 import PI0Policy
    from lerobot.configs.types import FeatureType, PolicyFeature
    policy = PI0Policy.from_pretrained("lerobot/pi0")



    policy.config.output_features = {
        "action": PolicyFeature(
            type=FeatureType.ACTION,
            shape=(14,)  
        )
    }
    
    policy.config.input_features = {
        "observation.images.top": PolicyFeature(
            type=FeatureType.VISUAL, 
            shape=(3, 224, 224)
        ),
        "observation.state": PolicyFeature(
            type=FeatureType.STATE,
            shape=(14,) 
        )
    }

In modeling_pi0.py, add the following:

In prepare_images, add:

        if not self.config.image_features and any('image' in key for key in batch.keys()):
                image_features = [key for key in batch.keys() if 'image' in key]
            else:
                image_features = self.config.image_features
            
        present_img_keys = [key for key in image_features if key in batch]
        missing_img_keys = [key for key in image_features if key not in batch]
In prepare_languages, add:

    if "task" in batch:
        tasks = batch["task"]
    else:

        batch_size = batch[OBS_ROBOT].shape[0]
        tasks = ["transfer cube"] * batch_size
then run：

    python lerobot/scripts/eval.py --policy.path=lerobot/pi0 --output_dir=outputs/eval/pi0_transfer_cube --env.type=aloha --env.task=AlohaTransferCube-v0 --eval.n_episodes=10 --eval.batch_size=2 --policy.device=cuda --policy.use_amp=false
