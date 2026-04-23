quick_dot_ai_layer_inc_abs = [meson.current_source_dir()]
quick_dot_ai_layer_inc = [include_directories('.')]

quick_dot_ai_rms_norm_src_abs = [meson.current_source_dir() / 'rms_norm.cpp']
quick_dot_ai_swiglu_src_abs = [meson.current_source_dir() / 'swiglu.cpp']
quick_dot_ai_tie_word_embedding_src_abs = [meson.current_source_dir() / 'tie_word_embedding.cpp']
quick_dot_ai_lmhead_src_abs = [meson.current_source_dir() / 'lm_head.cpp']
quick_dot_ai_mha_core_abs = [meson.current_source_dir()/ 'mha_core.cpp']
quick_dot_ai_embedding_src_abs = [meson.current_source_dir() / 'embedding_layer.cpp']
quick_dot_ai_reshaped_rms_norm_src_abs = [meson.current_source_dir() / 'reshaped_rms_norm.cpp']
quick_dot_ai_qkv_layer_src_abs = [meson.current_source_dir() / 'qkv_layer.cpp']
quick_dot_ai_embedding_pooling_src_abs = [meson.current_source_dir() / 'embedding_pooling_layer.cpp']
quick_dot_ai_embedding_normalize_src_abs = [meson.current_source_dir() / 'embedding_normalize_layer.cpp']

openmp_dep = dependency('openmp')
quick_dot_ai_rms_norm = shared_library(
    'quick_dot_ai_rms_norm_layer', 
    quick_dot_ai_rms_norm_src_abs, 
    include_directories: quick_dot_ai_layer_inc, 
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep],
    install: true,
    install_dir: application_install_dir
)
quick_dot_ai_rms_norm_dep = declare_dependency(
    link_with: quick_dot_ai_rms_norm,
    include_directories: quick_dot_ai_layer_inc
)

quick_dot_ai_swiglu = shared_library(
    'quick_dot_ai_swiglu_layer',
    quick_dot_ai_swiglu_src_abs,
    include_directories: quick_dot_ai_layer_inc,
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep],
    install: true,
    install_dir: application_install_dir
)
quick_dot_ai_swiglu_dep = declare_dependency(
    link_with: quick_dot_ai_swiglu,
    include_directories: quick_dot_ai_layer_inc
)

quick_dot_ai_tie_word_embedding = shared_library(
    'quick_dot_ai_tie_word_embedding_layer',
    quick_dot_ai_tie_word_embedding_src_abs,
    include_directories: quick_dot_ai_layer_inc,
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep, openmp_dep],
    install: true,
    install_dir: application_install_dir
)
quick_dot_ai_tie_word_embedding_dep = declare_dependency(
    link_with: quick_dot_ai_tie_word_embedding,
    include_directories: quick_dot_ai_layer_inc
)

quick_dot_ai_lm_head = shared_library(
    'quick_dot_ai_lm_head',
    quick_dot_ai_lmhead_src_abs,
    include_directories: quick_dot_ai_layer_inc,
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep],
    install: true,
    install_dir: application_install_dir
)
quick_dot_ai_lm_head_dep = declare_dependency(
    link_with: quick_dot_ai_lm_head,
    include_directories: quick_dot_ai_layer_inc
)

quick_dot_ai_mha_core = shared_library(
    'quick_dot_ai_mha_core_layer',
    quick_dot_ai_mha_core_abs,
    include_directories: quick_dot_ai_layer_inc,
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep, openmp_dep],
    install: true,
    install_dir: application_install_dir
)
quick_dot_ai_mha_core_dep = declare_dependency(
    link_with: quick_dot_ai_mha_core,
    include_directories: quick_dot_ai_layer_inc
)

quick_dot_ai_embedding_layer = shared_library(
    'quick_dot_ai_embedding_layer',
    quick_dot_ai_embedding_src_abs,
    include_directories: quick_dot_ai_layer_inc,
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep, openmp_dep],
    install: true,
    install_dir: application_install_dir
)
quick_dot_ai_embedding_layer_dep = declare_dependency(
    link_with: quick_dot_ai_embedding_layer,
    include_directories: quick_dot_ai_layer_inc
)

quick_dot_ai_embedding_pooling_layer = shared_library(
    'quick_dot_ai_embedding_pooling_layer',
    quick_dot_ai_embedding_pooling_src_abs,
    include_directories: quick_dot_ai_layer_inc,
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep, openmp_dep],
    install: true,
    install_dir: application_install_dir
)
quick_dot_ai_embedding_pooling_layer_dep = declare_dependency(
    link_with: quick_dot_ai_embedding_pooling_layer,
    include_directories: quick_dot_ai_layer_inc
)

quick_dot_ai_embedding_normalize_layer = shared_library(
    'quick_dot_ai_embedding_normalize_layer',
    quick_dot_ai_embedding_normalize_src_abs,
    include_directories: quick_dot_ai_layer_inc,
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep],
    install: true,
    install_dir: application_install_dir
)

quick_dot_ai_embedding_normalize_layer_dep = declare_dependency(
    link_with: quick_dot_ai_embedding_normalize_layer,
    include_directories: quick_dot_ai_layer_inc
)

quick_dot_ai_reshaped_rms_norm = shared_library(
    'quick_dot_ai_reshaped_rms_norm_layer', 
    quick_dot_ai_reshaped_rms_norm_src_abs, 
    include_directories: quick_dot_ai_layer_inc, 
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep],
    install: true,
    install_dir: application_install_dir
)
quick_dot_ai_reshaped_rms_norm_dep = declare_dependency(
    link_with: quick_dot_ai_reshaped_rms_norm,
    include_directories: quick_dot_ai_layer_inc
)

quick_dot_ai_qkv_layer = shared_library(
    'quick_dot_ai_qkv_layer',
    quick_dot_ai_qkv_layer_src_abs,
    include_directories: quick_dot_ai_layer_inc,
    dependencies: [nntrainer_dep, nntrainer_ccapi_dep],
    install: true,
    install_dir: application_install_dir
)

quick_dot_ai_qkv_layer_dep = declare_dependency(
    link_with: quick_dot_ai_qkv_layer,
    include_directories: quick_dot_ai_layer_inc
)
