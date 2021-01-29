# List of 123 Executors in Jina

This version of Jina includes 123 Executors.

## Inheritances in a Tree View
- `JAMLCompatible`
   - `BaseExecutor`
      - `BaseEvaluator`
         - `BaseRankingEvaluator`
            - `ReciprocalRankEvaluator`
            - `f1ScoreEvaluator`
            - `AveragePrecisionEvaluator`
            - `NDCGEvaluator`
         - `BaseTextEvaluator`
            - `BleuEvaluator`
            - `EditDistanceEvaluator`
            - `RougeEvaluator`
            - `JaccardSimilarityEvaluator`
            - `HammingDistanceEvaluator`
            - `GleuEvaluator`
         - `BaseEmbeddingEvaluator`
            - `MinkowskiDistanceEvaluator`
            - `L1NormEvaluator`
            - `InfiniteNormEvaluator`
      - `BaseIndexer`
         - `BaseKVIndexer`
            - `BinaryPbIndexer`
               - `RedisDBIndexer`
               - `MongoDBIndexer`
               - `LevelDBIndexer`
         - `BaseVectorIndexer`
            - `BaseNumpyIndexer`
               - `AnnoyIndexer`
               - `SptagIndexer`
               - `NGTIndexer`
               - `NumpyIndexer`
                  - `ZarrIndexer`
               - `NmsLibIndexer`
               - `ScannIndexer`
               - `BaseDevice`
                  - `FaissDevice`
                     - `FaissIndexer`
            - `MilvusIndexer`
      - `BaseEncoder`
         - `BaseNumericEncoder`
            - `BaseAudioEncoder`
               - `BaseDevice`
                  - `TorchDevice`
                     - `BaseTorchEncoder`
                        - `Wav2VecSpeechEncoder`
               - `ChromaPitchEncoder`
               - `MFCCTimbreEncoder`
            - `BaseVideoEncoder`
               - `BaseDevice`
                  - `TorchDevice`
                     - `BaseTorchEncoder`
                        - `VideoTorchEncoder`
            - `TransformEncoder`
               - `FeatureAgglomerationEncoder`
               - `RandomGaussianEncoder`
               - `IncrementalPCAEncoder`
               - `FastICAEncoder`
               - `RandomSparseEncoder`
            - `BaseDevice`
               - `TFDevice`
                  - `CompressionVaeEncoder`
            - `TSNEEncoder`
         - `BaseDevice`
            - `TorchDevice`
               - `BaseTorchEncoder`
                  - `VSETextEncoder`
                  - `ImageTorchEncoder`
                  - `FarmTextEncoder`
                  - `VSEImageEncoder`
                  - `CustomImageTorchEncoder`
                  - `TirgImageEncoder`
                  - `LaserEncoder`
                  - `FlairTextEncoder`
               - `TransformerTorchEncoder`
            - `PaddleDevice`
               - `BasePaddleEncoder`
                  - `ImagePaddlehubEncoder`
                  - `VideoPaddleEncoder`
                  - `TextPaddlehubEncoder`
            - `TFDevice`
               - `BaseTFEncoder`
                  - `BigTransferEncoder`
                  - `ImageKerasEncoder`
                  - `CustomKerasImageEncoder`
               - `TransformerTFEncoder`
            - `OnnxDevice`
               - `BaseOnnxEncoder`
                  - `ImageOnnxEncoder`
         - `BaseTextEncoder`
            - `OneHotTextEncoder`
      - `BaseSegmenter`
         - `JiebaSegmenter`
         - `AudioSlicer`
         - `Sentencizer`
         - `DeepSegmenter`
         - `FiveImageCropper`
         - `BaseDevice`
            - `TorchDevice`
               - `TorchObjectDetectionSegmenter`
         - `SlidingWindowImageCropper`
         - `PDFExtractorSegmenter`
         - `SlidingWindowSegmenter`
         - `RandomImageCropper`
         - `SlidingWindowAudioSlicer`
      - `BaseCrafter`
         - `AudioReader`
         - `ArrayBytesReader`
         - `ImageNormalizer`
         - `TikaExtractor`
         - `ImageResizer`
         - `ImageReader`
         - `ArrayStringReader`
         - `AlbumentationsCrafter`
         - `ImageCropper`
         - `CenterImageCropper`
         - `ImageFlipper`
         - `AudioNormalizer`
         - `AudioMonophoner`
      - `BaseRanker`
         - `Chunk2DocRanker`
            - `TfIdfRanker`
            - `BM25Ranker`
            - `SimpleAggregateRanker`
            - `BiMatchRanker`
         - `Match2DocRanker`
            - `LevenshteinRanker`
      - `BaseMultiModalEncoder`
         - `BaseDevice`
            - `TorchDevice`
               - `TirgMultiModalEncoder`

## Modules in a Table View 

| Class | Module |
| --- | --- |
| `AlbumentationsCrafter` | `jina.hub.crafters.audio.AudioMonophoner` |
| `AnnoyIndexer` | `jina.hub.indexers.vector.FaissIndexer` |
| `ArrayBytesReader` | `jina.hub.crafters.audio.AudioMonophoner` |
| `ArrayStringReader` | `jina.hub.crafters.audio.AudioMonophoner` |
| `AudioMonophoner` | `jina.hub.crafters.audio.AudioMonophoner` |
| `AudioNormalizer` | `jina.hub.crafters.audio.AudioMonophoner` |
| `AudioReader` | `jina.hub.crafters.audio.AudioMonophoner` |
| `AudioSlicer` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `AveragePrecisionEvaluator` | `jina.hub.evaluators.rank.NdcgEvaluator` |
| `BM25Ranker` | `jina.hub.rankers.BiMatchRanker` |
| `BaseAudioEncoder` | `jina.hub.encoders.numeric.TSNEEncoder` |
| `BaseCrafter` |   |
| `BaseDevice` | `jina.hub.encoders.audio.MFCCTimbreEncoder` |
| `BaseDevice` | `jina.hub.encoders.multimodal.TirgMultimodalEncoder` |
| `BaseDevice` | `jina.hub.encoders.nlp.TransformerTFEncoder` |
| `BaseDevice` | `jina.hub.encoders.numeric.TSNEEncoder` |
| `BaseDevice` | `jina.hub.encoders.video.VideoTorchEncoder` |
| `BaseDevice` | `jina.hub.indexers.vector.FaissIndexer` |
| `BaseDevice` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `BaseEmbeddingEvaluator` |   |
| `BaseEncoder` |   |
| `BaseEvaluator` |   |
| `BaseExecutor` |   |
| `BaseIndexer` |   |
| `BaseKVIndexer` |   |
| `BaseMultiModalEncoder` |   |
| `BaseNumericEncoder` | `jina.hub.encoders.nlp.TransformerTFEncoder` |
| `BaseNumpyIndexer` | `jina.hub.indexers.vector.MilvusIndexer` |
| `BaseOnnxEncoder` |   |
| `BasePaddleEncoder` |   |
| `BaseRanker` |   |
| `BaseRankingEvaluator` |   |
| `BaseSegmenter` |   |
| `BaseTFEncoder` |   |
| `BaseTextEncoder` | `jina.hub.encoders.nlp.TransformerTFEncoder` |
| `BaseTextEvaluator` |   |
| `BaseTorchEncoder` |   |
| `BaseVectorIndexer` |   |
| `BaseVideoEncoder` | `jina.hub.encoders.numeric.TSNEEncoder` |
| `BiMatchRanker` | `jina.hub.rankers.BiMatchRanker` |
| `BigTransferEncoder` | `jina.hub.encoders.image.CustomKerasImageEncoder` |
| `BinaryPbIndexer` |   |
| `BleuEvaluator` | `jina.hub.evaluators.text.GleuEvaluator` |
| `CenterImageCropper` | `jina.hub.crafters.audio.AudioMonophoner` |
| `ChromaPitchEncoder` | `jina.hub.encoders.audio.MFCCTimbreEncoder` |
| `Chunk2DocRanker` |   |
| `CompressionVaeEncoder` |   |
| `CustomImageTorchEncoder` | `jina.hub.encoders.nlp.FlairTextEncoder` |
| `CustomKerasImageEncoder` | `jina.hub.encoders.image.CustomKerasImageEncoder` |
| `DeepSegmenter` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `EditDistanceEvaluator` | `jina.hub.evaluators.text.GleuEvaluator` |
| `FaissDevice` |   |
| `FaissIndexer` |   |
| `FarmTextEncoder` | `jina.hub.encoders.nlp.FlairTextEncoder` |
| `FastICAEncoder` | `jina.hub.encoders.numeric.RandomSparseEncoder` |
| `FeatureAgglomerationEncoder` | `jina.hub.encoders.numeric.RandomSparseEncoder` |
| `FiveImageCropper` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `FlairTextEncoder` | `jina.hub.encoders.nlp.FlairTextEncoder` |
| `GleuEvaluator` | `jina.hub.evaluators.text.GleuEvaluator` |
| `HammingDistanceEvaluator` | `jina.hub.evaluators.text.GleuEvaluator` |
| `ImageCropper` | `jina.hub.crafters.audio.AudioMonophoner` |
| `ImageFlipper` | `jina.hub.crafters.audio.AudioMonophoner` |
| `ImageKerasEncoder` | `jina.hub.encoders.image.CustomKerasImageEncoder` |
| `ImageNormalizer` | `jina.hub.crafters.audio.AudioMonophoner` |
| `ImageOnnxEncoder` | `jina.hub.encoders.image.ImageOnnxEncoder` |
| `ImagePaddlehubEncoder` | `jina.hub.encoders.nlp.TextPaddlehubEncoder` |
| `ImageReader` | `jina.hub.crafters.audio.AudioMonophoner` |
| `ImageResizer` | `jina.hub.crafters.audio.AudioMonophoner` |
| `ImageTorchEncoder` | `jina.hub.encoders.nlp.FlairTextEncoder` |
| `IncrementalPCAEncoder` | `jina.hub.encoders.numeric.RandomSparseEncoder` |
| `InfiniteNormEvaluator` | `jina.hub.evaluators.embedding.InfiniteNormEvaluator` |
| `JAMLCompatible` |   |
| `JaccardSimilarityEvaluator` | `jina.hub.evaluators.text.GleuEvaluator` |
| `JiebaSegmenter` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `L1NormEvaluator` | `jina.hub.evaluators.embedding.InfiniteNormEvaluator` |
| `LaserEncoder` | `jina.hub.encoders.nlp.FlairTextEncoder` |
| `LevelDBIndexer` | `jina.hub.indexers.keyvalue.LevelDBIndexer` |
| `LevenshteinRanker` | `jina.hub.rankers.LevenshteinRanker` |
| `MFCCTimbreEncoder` | `jina.hub.encoders.audio.MFCCTimbreEncoder` |
| `Match2DocRanker` |   |
| `MilvusIndexer` | `jina.hub.indexers.vector.MilvusIndexer` |
| `MinkowskiDistanceEvaluator` | `jina.hub.evaluators.embedding.InfiniteNormEvaluator` |
| `MongoDBIndexer` | `jina.hub.indexers.keyvalue.LevelDBIndexer` |
| `NDCGEvaluator` | `jina.hub.evaluators.rank.NdcgEvaluator` |
| `NGTIndexer` | `jina.hub.indexers.vector.FaissIndexer` |
| `NmsLibIndexer` | `jina.hub.indexers.vector.FaissIndexer` |
| `NumpyIndexer` | `jina.hub.indexers.vector.FaissIndexer` |
| `OneHotTextEncoder` | `jina.hub.encoders.nlp.OneHotTextEncoder` |
| `OnnxDevice` |   |
| `PDFExtractorSegmenter` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `PaddleDevice` |   |
| `RandomGaussianEncoder` | `jina.hub.encoders.numeric.RandomSparseEncoder` |
| `RandomImageCropper` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `RandomSparseEncoder` | `jina.hub.encoders.numeric.RandomSparseEncoder` |
| `ReciprocalRankEvaluator` | `jina.hub.evaluators.rank.NdcgEvaluator` |
| `RedisDBIndexer` | `jina.hub.indexers.keyvalue.LevelDBIndexer` |
| `RougeEvaluator` | `jina.hub.evaluators.text.GleuEvaluator` |
| `ScannIndexer` | `jina.hub.indexers.vector.FaissIndexer` |
| `Sentencizer` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `SimpleAggregateRanker` | `jina.hub.rankers.BiMatchRanker` |
| `SlidingWindowAudioSlicer` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `SlidingWindowImageCropper` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `SlidingWindowSegmenter` | `jina.hub.segmenters.audio.SlidingWindowAudioSlicer` |
| `SptagIndexer` | `jina.hub.indexers.vector.FaissIndexer` |
| `TFDevice` |   |
| `TSNEEncoder` | `jina.hub.encoders.numeric.TSNEEncoder` |
| `TextPaddlehubEncoder` | `jina.hub.encoders.nlp.TextPaddlehubEncoder` |
| `TfIdfRanker` | `jina.hub.rankers.BiMatchRanker` |
| `TikaExtractor` | `jina.hub.crafters.audio.AudioMonophoner` |
| `TirgImageEncoder` | `jina.hub.encoders.nlp.FlairTextEncoder` |
| `TirgMultiModalEncoder` |   |
| `TorchDevice` |   |
| `TorchObjectDetectionSegmenter` |   |
| `TransformEncoder` | `jina.hub.encoders.numeric.TSNEEncoder` |
| `TransformerTFEncoder` |   |
| `TransformerTorchEncoder` |   |
| `VSEImageEncoder` | `jina.hub.encoders.nlp.FlairTextEncoder` |
| `VSETextEncoder` | `jina.hub.encoders.nlp.FlairTextEncoder` |
| `VideoPaddleEncoder` | `jina.hub.encoders.nlp.TextPaddlehubEncoder` |
| `VideoTorchEncoder` |   |
| `Wav2VecSpeechEncoder` |   |
| `ZarrIndexer` | `jina.hub.indexers.vector.ZarrIndexer` |
| `f1ScoreEvaluator` | `jina.hub.evaluators.rank.NdcgEvaluator` |